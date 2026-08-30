# AGENTS.md — AhaPi

Instructions for coding agents working in this repository. Read it through before editing any file.

## 1. What this project is

AhaPi is a **static site** that publishes maths lessons. Each lesson carries a YouTube video, theory
written in Markdown + LaTeX, a vocabulary list with pronunciation buttons, and exercises whose
answers stay hidden until the student asks for them.

- There is **no** build step, package manager, bundler, test runner, CI, backend, database or login.
- The whole site is 2 HTML files + 1 JSON file + a folder of `.md`. Deploying is `git push` to GitHub Pages.
- Live: https://pvd0anh.github.io/AhaPi/

**The real user of this repository is a teacher who does not program.** They author lessons in
`editor.html` and upload files through GitHub's web interface — no terminal, no git CLI. Every change
must preserve that workflow. A feature that is "technically elegant" but requires them to run a
command is a broken feature.

## 2. File map

| File | Role |
|---|---|
| `index.html` | The student-facing app. CSS, HTML and JS in one file |
| `editor.html` | Lesson editor for the teacher, runs offline from `file://` |
| `index.json` | Table of contents. **Array order = teaching order** |
| `lessons/*.md` | 23 lessons, one file each, in the `[[TAG]]` format of §5 |
| `images/` | Pictures used inside lessons |
| `README.md` | Technical documentation, for developers |
| `AUTHORING.md` | Step-by-step lesson-authoring guide, written for a non-technical teacher |
| `404.html`, `favicon.svg` | Error page + icon |
| `.nojekyll` | **Must not be deleted** (§4.1) |

External dependencies, all via CDN, all version-pinned:
`katex@0.16.9` (css + js), `marked@11.1.1`, Google Fonts (Be Vietnam Pro, Source Serif 4, IBM Plex Mono).

## 3. Running it locally

```bash
python3 -m http.server 8000     # then open http://localhost:8000/
```

- `index.html` **must** be served over http(s) — it calls `fetch('index.json')`, which the browser
  blocks under `file://`, and the page then reports "Chưa đọc được danh sách bài".
- `editor.html` is the opposite: double-clicking it to open over `file://` works, and that is how the
  teacher uses it. Do not add anything to it that requires a server.

There is no test suite and no linter. Verification means opening a browser, plus the checks in §12.

## 4. Invariants — break these and the site fails silently

### 4.1 `.nojekyll`
GitHub Pages runs Jekyll by default, and Jekyll will not serve `lessons/*.md` as static files.
Delete `.nojekyll` and every lesson 404s while the site still builds "successfully".

### 4.2 `id` ↔ filename ↔ date
For each row in `index.json`:
- `lessons/<id>.md` must exist. A missing file shows an error in the page; it does not crash.
- `id` has the form `YYYY-MM-DD-<slug>`, and the `date` field is **`DD/MM/YYYY`** of that same day.
  All 23 lessons currently agree on this — keep it that way.
- A lesson absent from `index.json` simply does not appear. Nothing reports it anywhere.

### 4.3 Array order in `index.json` is the teaching order
`renderTree()` groups lessons by their `chapter` field **in order of first appearance**, and the
Previous / Next buttons walk the array index. Re-sorting the array rewrites the syllabus. Never
reorder it on your own initiative.

Chapter names follow the pattern `Chương N · Name` — the separator is **U+00B7 MIDDLE DOT**, not a
full stop or a hyphen. The `chapter` string must match **byte for byte** across lessons in the same
chapter; one stray character splits the sidebar into two chapters.

### 4.4 Maths is extracted *before* Markdown runs
`render()` (present in both HTML files) does exactly this, in this order:

1. replace `$$…$$`, then `$…$`, with the placeholder `@@M{n}@@`, pushing the contents onto `store`
2. `marked.parse()`
3. replace each placeholder with `katex.renderToString()`

That is what stops Markdown from eating `\\`, `_` and `*` inside a formula. **Do not reorder these
steps**, and do not swap in KaTeX auto-render.

Consequences worth remembering when authoring:
- the inline regex is `/\$([^$\n]+?)\$/g` → an inline formula **cannot span lines**, and two stray
  `$` on one line will be read as a formula.
- KaTeX runs with `throwOnError:true`; a syntax error **does not** break the page, it renders as red
  text in `<code class="math-error">`. So a malformed formula slips through if you only watch the console.

### 4.5 Lesson Markdown is not sanitised
`marked` runs with its defaults, so raw HTML in a `.md` really is rendered. No lesson currently uses
raw HTML. Lesson content is trusted content (the teacher writes it), which makes this acceptable —
but **do not open any path for untrusted content to reach `render()`**.

Data from `index.json` and the vocabulary list does go through `esc()` in `index.html`. The preview
pane in `editor.html` escapes **nothing** — acceptable because it runs locally, but do not copy that
pattern into `index.html`.

### 4.6 `localStorage`
Exactly two keys. Renaming them wipes the progress of every student already using the site, so both
reads fall back to the legacy key one last time:

| Key | Contents | Legacy key still read |
|---|---|---|
| `ahapi:progress` | JSON array of `id`s marked as studied | `ahapi-da-hoc` |
| `ahapi:lang` | `'en'` \| `'both'` \| `'vi'` | `ahapi-ngon-ngu` (values `en`/`song`/`vi`) |

Writes always go to the new key. Do not remove the fallback reads until every student's browser has
been through the site at least once.

## 5. Lesson file grammar

The parser is `parseLesson()` in `index.html`. It is **not** extended Markdown, it is a handful of
string slices. Every tag is optional, but its position is not.

```markdown
[[VIDEO]] https://youtu.be/xxxxxxxxxxx
[[TITLE VI]] Vietnamese lesson title

## Theory
... Markdown + $LaTeX$ ...

[[VOCABULARY]]
ratio /ˈreɪʃiəʊ/ : tỉ số

[[EXERCISES]]

[[Q]] Question text
[[VI]] Question text in Vietnamese
[[A]] Answer
[[VI]] Answer in Vietnamese
```

The exact rules:

- `[[VIDEO]]` and `[[TITLE VI]]` must each **sit alone on their own line**. The video link must be a
  single whitespace-free token (`\S+`) — pasting a link with a trailing note breaks it.
- `[[VOCABULARY]]` and `[[EXERCISES]]` are located with `indexOf` → only the **first** occurrence
  counts, and **`[[VOCABULARY]]` must come before `[[EXERCISES]]`**. Reversed, the vocabulary block
  gets swallowed into the final answer.
- Theory is everything before whichever of those two tags appears first.
- Everything after `[[EXERCISES]]` is exercises, split on `[[Q]]`, each question split again on `[[A]]`.
- A vocabulary line splits at its **first** `:`. The IPA is the first `/…/` group on the left-hand
  side and is stripped out of the word. A line with no `:`, or with either side empty, is
  **dropped silently**.

### Bilingual markup

Inside the theory, `[[EN]]` / `[[VI]]` / `[[SHARED]]` must **start a line** (`splitPairs` uses a
regex anchored with `^` and the `m` flag). Inside exercises it is the opposite — `[[VI]]` splits
inline anywhere (`splitInline`), and there is **one `[[VI]]` per side** only.

How `splitPairs()` behaves:

| Case | Result |
|---|---|
| text before the first tag | becomes a `shared` block, full width |
| `[[EN]]` … `[[VI]]` … | one pair, two columns starting level |
| `[[EN]]` with no `[[VI]]` | demoted to `shared` — a half-translated lesson still reads correctly |
| orphan `[[VI]]` (no `[[EN]]` immediately before) | becomes `shared`, **no** error |
| `[[SHARED]]` | full width |

A lesson counts as bilingual when it has **at least one pair**, or any exercise carries a `[[VI]]`.
`[[TITLE VI]]` on its own is **not** enough to show the language picker.

## 6. Code duplicated across the two HTML files — risk source number one

`index.html` and `editor.html` each keep their **own copy** of:

`marked.use({renderer:{image}})` · `render()` · `youtubeId()` · `splitPairs()` · `hasPair` ·
`renderPair()` · `renderMaybePair()` · `renderBody()`

No build step keeps them in sync. **Change one and you must change the other**, or the teacher's
preview starts lying about what the student sees.

Some things differ **on purpose** — do not "fix" them without asking:

| | `index.html` | `editor.html` |
|---|---|---|
| YouTube embed | `hqdefault` image facade → `youtube-nocookie.com` on click | `<iframe>` on `youtube.com/embed` immediately |
| HTML escaping | has `esc()` | none |
| accent variable | `--accent` | `--accent` (separate blackboard palette) |
| remembers language choice | yes, in `localStorage` | no, in-memory `lang` |
| bilingual condition | `hasPair(blocks) \|\| exercises.some(...)` | adds `\|\| !!d.titleVi` |

That last row is a **genuine divergence**: a lesson with only a Vietnamese title shows the language
picker in the preview but not on the student page. If you fix it, fix it in both files.

If `buildMarkdown()` in `editor.html` changes how it emits files, `parseLesson()` in `index.html`
must change at the same time — and vice versa. The 23 existing lessons will not migrate themselves.

## 7. Code conventions

- **Identifiers, comments and CSS names are English.** Keep it that way. The student-facing UI
  strings stay Vietnamese — that is deliberate, not an oversight, and the two must not be conflated.
- Comments are English, short, and explain **why**, placed at end of line or above a block.
- Vanilla JS. No framework, no modules, no `import`. `const $=id=>document.getElementById(id)`.
- Style: dense, sparing with whitespace, plenty of one-line expressions. Write to match the
  surrounding code.
- CSS: all of it in the `<style>` block of the same file, sectioned with `/* ===== SECTION ===== */`
  banners, colours and sizes drawn from `:root` variables. Do not add separate CSS/JS files.
- UI state is pushed onto `document.body` (`data-lang`, `.sidebar-collapsed`, `.sidebar-open`) and
  CSS handles the rest.
- Preserve the accessibility already in place: `aria-expanded`, `aria-pressed`, `aria-label`,
  `:focus-visible`, `@media(prefers-reduced-motion:reduce)`.
- Breakpoints: **1024px** (sidebar becomes an overlay, `--sidebar-w:0`) and **900px** (bilingual
  columns stack).

## 8. Lesson content conventions

A new lesson should look like the 23 that already exist:

- Use `##` and `###` only. **Never `#`** — the lesson title already comes from `index.json`.
- Exactly **3 questions** under `[[EXERCISES]]`; the last is usually a "spot the mistake" question.
- Every lesson carries at least one `> **Ghi nhớ:** …` callout (23/23 currently do).
- Tables are used heavily (14/23 lessons) to contrast notations or cases.
- Vocabulary: present in 17/23 lessons, around 5–7 words, IPA taken from Oxford.
- **Only 1/23 lessons is bilingual** (`2026-09-01-ratio-definition-1.md`) — it is the reference
  example for `[[EN]]`/`[[VI]]` structure. Monolingual is still the default.
- Common LaTeX: `\frac` `\dfrac` `\times` `\div` `\cdot` `\circ` `\widehat` `\parallel` `\perp`
  `\text` `\qquad` `\Longrightarrow`, plus `\begin{array}{r}…\hline…\end{array}` for column arithmetic.
- Images: `![Caption](images/file-name.png)`, path from the site root. The caption renders below the
  picture. Image filenames are lowercase ASCII, no accents, no spaces. (No lesson uses an image yet.)
- UTF-8, no BOM, LF line endings.

## 9. Documentation — two different readers

- `README.md`: for developers. Tight prose, tables, no emoji, no marketing.
- `AUTHORING.md`: the lesson-authoring workflow, for a teacher who does not program. "Step / What to
  do" tables, imperative sentences, button names quoted exactly as they appear on screen. Note that
  the interface itself is in Vietnamese, so button names appear in Vietnamese inside an otherwise
  English document — that is correct, do not translate them.

Any feature touching the authoring workflow **must update both files**.

## 10. Deploying

Push to `main`; GitHub Pages serves from the root. No build step, no action. Live in 1–2 minutes.
Netlify/Vercel work too — drag the folder in, no configuration.

Commit messages: English, imperative mood, one line, no Conventional Commits prefix.

## 11. Do not do the following (unless explicitly asked)

- Add `package.json`, npm, a bundler, TypeScript, a framework, or any build step.
- Split `index.html` into separate `.js` / `.css` files. One file per page is a deliberate design choice.
- Rename `index.html`, `editor.html`, `index.json`, `lessons/`, `images/` — `AUTHORING.md` walks the
  teacher through those exact names.
- Delete `.nojekyll`.
- Re-sort `index.json`.
- Bulk-edit files in `lessons/` with a script — that content is authored work, not data.
- Unpin a CDN version, or add a new CDN (this project's effective CSP is "whatever is already here").
- Change the `localStorage` keys, or drop the legacy-key fallback reads.
- Translate the student-facing UI strings. They are Vietnamese by design (§7).

Changing the accent colour means editing **all four places**: `--accent` in `index.html`, `--accent`
in `editor.html`, the two hardcoded `#5B3FA8` in `404.html`, and `fill` in `favicon.svg`.
Changing the product name means editing `<title>`, the header, `document.title`, `editor.html` and
`404.html`; the `π` logo lives in `index.html`, `404.html` and `favicon.svg`.

## 12. Quick verification commands

```bash
# index.json id ↔ .md file ↔ date
python3 - <<'PY'
import json, os
rows  = json.load(open('index.json'))
ids   = [r['id'] for r in rows]
files = {f[:-3] for f in os.listdir('lessons') if f.endswith('.md')}
print('missing file :', [i for i in ids if i not in files])
print('orphan file  :', sorted(files - set(ids)))
print('duplicate id :', [i for i in ids if ids.count(i) > 1])
print('date mismatch:', [r['id'] for r in rows
                         if '/'.join(reversed(r['id'][:10].split('-'))) != r['date']])
PY

# [[VOCABULARY]] must come before [[EXERCISES]]
for f in lessons/*.md; do
  v=$(grep -n 'VOCABULARY' "$f" | cut -d: -f1); e=$(grep -n 'EXERCISES' "$f" | cut -d: -f1)
  [ -n "$v" ] && [ -n "$e" ] && [ "$v" -gt "$e" ] && echo "WRONG ORDER: $f"
done

# tag census
grep -ho '\[\[[^]]*\]\]' lessons/*.md | sort | uniq -c | sort -rn

# images referenced by a lesson but absent from images/
grep -oh 'images/[^)]*' lessons/*.md | sort -u | while read p; do [ -f "$p" ] || echo "MISSING: $p"; done

# unclosed inline formula (the number of $ on a line must be even)
awk '{n=gsub(/\$/,"$")} n%2 {print FILENAME":"FNR": odd number of $"}' lessons/*.md

# no Vietnamese identifiers crept back into the code
grep -nE '\-\-(nhan|chu|vien|nen|giay|do|xanh)|function (ve|tach|phan|lay|tao)[A-Z]' index.html editor.html
```
