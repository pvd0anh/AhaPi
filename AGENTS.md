# AGENTS.md — AhaPi

## Context

Static maths-lesson site on GitHub Pages, custom domain `thaole.io.vn` (`CNAME`). No build step,
package manager, tests, CI or backend; deploying is a push to `main`.

The maintainers are a teacher and helpers who **do not program**. They author lessons in
`editor.html` (opened from `file://`) and upload files through github.com. Every change must keep
that workflow command-free.

## File map

| Path | Role |
|---|---|
| `index.html` | Home page. Lesson card with progress, model cards from `models/models.json`, editor link. Redirects legacy `#<lesson-id>` to `lessons.html` |
| `lessons.html` | Student lesson app (CSS + HTML + JS in one file). Needs http(s): it `fetch`es `index.json` |
| `editor.html` | Teacher's lesson editor with live preview. Must keep working from `file://` |
| `index.json` | Lesson table of contents. **Array order = teaching order** |
| `lessons/<id>.md` | One lesson per file, `[[TAG]]` format below |
| `images/` | Pictures used inside lessons |
| `models/*.html` | Standalone interactive pages, authored by others. Leave their code and styling as is |
| `models/models.json` | Home-page cards for `models/*.html`. **Array order = card order**. Hand-edited on github.com |
| `models/art/` | Card pictures referenced from `models.json` |
| `models/README.md` | Vietnamese how-to for teachers adding a model; GitHub renders it under the folder listing. Keep it in step with `AUTHORING.md` §F |
| `404.html`, `favicon.svg` | Error page, icon |
| `.nojekyll`, `CNAME` | Required by GitHub Pages — keep both |
| `README.md` / `AUTHORING.md` | Docs for developers / for the non-programming teacher |

CDN dependencies, version-pinned: `katex@0.16.9`, `marked@11.1.1`, Google Fonts. Keep that set and
those pins as they are.

Run locally: `python3 -m http.server 8000`.

## Invariants

- **`.nojekyll`** stops Jekyll from hiding `lessons/*.md`; without it every lesson 404s while the
  build still "succeeds". **`CNAME`** holds the custom domain.
- **`index.json` rows**: `id` is `YYYY-MM-DD-<slug>`, `date` is the same day as `DD/MM/YYYY`, and
  `lessons/<id>.md` exists. A lesson missing from `index.json` is silently invisible.
- **Order**: `renderTree()` groups by `chapter` in order of first appearance; Previous/Next walk the
  array. Preserve array order in both JSON files.
- **Chapter names**: `Chương N · Name`, separator U+00B7 MIDDLE DOT, identical byte for byte within
  a chapter — one stray character splits the sidebar.
- **Maths before Markdown**: `render()` (in both HTML files) swaps `$$…$$` then `$…$` for
  `@@M{n}@@` placeholders, runs `marked.parse()`, then substitutes `katex.renderToString()`. Keep
  this order; it protects `\\`, `_`, `*`. Inline maths cannot span lines (`/\$([^$\n]+?)\$/g`). A
  KaTeX error renders as red `code.math-error`, not a console error.
- **Trust boundary**: lesson Markdown is unsanitised (teacher-written, trusted). Data from
  `index.json`, vocabulary and `models.json` goes through `esc()`. `editor.html` escapes nothing —
  fine locally; keep that pattern out of the served pages.
- **`models.json`**: `renderModels()` validates each entry (`file` must match `SAFE_FILE`, `image`
  `SAFE_IMG` — relative paths only) and shows a Vietnamese notice instead of a blank grid. Keep
  every failure visible and readable by a non-programmer.
- **`localStorage`** — two keys, each with a legacy key still read as fallback. Writes go to the new
  key. Renaming a key or dropping a fallback wipes students' progress.

  | Key | Contents | Legacy key | Read by |
  |---|---|---|---|
  | `ahapi:progress` | JSON array of studied `id`s | `ahapi-da-hoc` | `lessons.html`, `index.html` |
  | `ahapi:lang` | `en` \| `both` \| `vi` | `ahapi-ngon-ngu` (`en`/`song`/`vi`) | `lessons.html` |

## Lesson format

Parsed by `parseLesson()` in `lessons.html` with string slicing, not a Markdown extension.

```markdown
[[VIDEO]] https://youtu.be/xxxxxxxxxxx
[[TITLE VI]] Vietnamese title

## Theory with $LaTeX$

[[VOCABULARY]]
ratio /ˈreɪʃiəʊ/ : tỉ số

[[EXERCISES]]
[[Q]] Question [[VI]] Câu hỏi
[[A]] Answer [[VI]] Đáp án
```

- Every tag is optional; positions are not. `[[VIDEO]]` and `[[TITLE VI]]` sit alone on their
  line; the video URL is one `\S+` token.
- `[[VOCABULARY]]` and `[[EXERCISES]]` are found with `indexOf` (first occurrence only), and
  `[[VOCABULARY]]` must come first, otherwise it is swallowed into the last answer. Theory is
  everything before the first of the two.
- Vocabulary line: split at the first `:`; IPA is the first `/…/` on the left. Lines with no `:` or
  an empty side are dropped silently.
- Bilingual theory: `[[EN]]` / `[[VI]]` / `[[SHARED]]` must start a line. `[[EN]]` + `[[VI]]` makes
  a two-column pair; `[[EN]]` alone or an orphan `[[VI]]` becomes full-width shared. In exercises
  `[[VI]]` splits inline, once per side.
- The language picker shows when there is at least one pair or any exercise `[[VI]]`.
  `[[TITLE VI]]` alone does not count (see divergence below).

Content conventions: `##`/`###` only (the title comes from `index.json`); 3 exercises, the last
usually "spot the mistake"; at least one `> **Ghi nhớ:** …` callout; vocabulary ~5–7 words with
Oxford IPA; monolingual by default, `lessons/2026-09-01-ratio-definition-1.md` is the bilingual
reference. Image filenames are lowercase ASCII with hyphens. UTF-8, LF, no BOM. Treat `lessons/*.md`
as authored work: edit by hand, never with a bulk script.

## Code shared between `lessons.html` and `editor.html`

Both files carry their own copy of `marked.use({renderer:{image}})`, `render()`, `youtubeId()`,
`splitPairs()`, `hasPair`, `renderPair()`, `renderMaybePair()`, `renderBody()`. Change one, change
the other, or the editor preview drifts from the student page. Likewise `buildMarkdown()` (editor)
and `parseLesson()` (lessons) change together.

Intentional differences — ask before unifying:

| | `lessons.html` | `editor.html` |
|---|---|---|
| YouTube | `hqdefault` facade → `youtube-nocookie.com` on click | immediate `youtube.com/embed` iframe |
| Escaping | `esc()` | none |
| Language choice | persisted in `localStorage` | in-memory |
| Bilingual condition | pairs or exercise `[[VI]]` | also `!!d.titleVi` (known divergence; fix in both if fixing) |

## Conventions

- Identifiers, comments, CSS names and docs in English; student-facing UI strings in Vietnamese.
- Vanilla JS, no modules; `const $=id=>document.getElementById(id)`. Dense style matching the
  surrounding code; short comments that explain why.
- One file per page: CSS in the page's `<style>` with `/* ===== SECTION ===== */` banners, colours
  from `:root` variables, JS inline.
- UI state lives on `document.body` (`data-lang`, `.sidebar-collapsed`, `.sidebar-open`); CSS reacts.
- Keep existing a11y: `aria-expanded`, `aria-pressed`, `aria-label`, `:focus-visible`,
  `prefers-reduced-motion`.
- Breakpoints: 1024px (sidebar becomes overlay), 900px (bilingual columns stack).
- File and folder names are referenced step by step in `AUTHORING.md`; keep them stable.
- Any change to the authoring workflow updates both `README.md` and `AUTHORING.md`. `AUTHORING.md`
  uses "Step / What to do" tables and quotes Vietnamese button names exactly.
- Commit messages: English, imperative, one line, no Conventional Commits prefix.

Site-wide edits touch several files:
- **Accent colour**: `--accent` in `index.html`, `lessons.html`, `editor.html`; `.home-link b` in
  each `models/*.html`; two `#5B3FA8` in `404.html`; `fill` in `favicon.svg`; `DEFAULT_ART` in
  `index.html`.
- **Product name**: `<title>`, header, `document.title`, `editor.html`, `404.html`. The `π` logo:
  `index.html`, `lessons.html`, `404.html`, `favicon.svg`.

## Verification

No test suite: open the pages in a browser, then run:

```bash
# index.json id ↔ file ↔ date
python3 - <<'PY'
import json, os
rows=json.load(open('index.json')); ids=[r['id'] for r in rows]
files={f[:-3] for f in os.listdir('lessons') if f.endswith('.md')}
print('missing file :', [i for i in ids if i not in files])
print('orphan file  :', sorted(files-set(ids)))
print('duplicate id :', [i for i in ids if ids.count(i)>1])
print('date mismatch:', [r['id'] for r in rows if '/'.join(reversed(r['id'][:10].split('-')))!=r['date']])
PY

# models.json parses and every file/image exists
python3 -c "import json,os;[print('MISSING:',p) for m in json.load(open('models/models.json')) for p in ['models/'+m['file']]+(['models/'+m['image']] if m.get('image') else []) if not os.path.exists(p)]"

# [[VOCABULARY]] before [[EXERCISES]]
for f in lessons/*.md; do v=$(grep -n 'VOCABULARY' "$f"|cut -d: -f1); e=$(grep -n 'EXERCISES' "$f"|cut -d: -f1)
  [ -n "$v" ] && [ -n "$e" ] && [ "$v" -gt "$e" ] && echo "WRONG ORDER: $f"; done

# lesson images that do not exist
grep -oh 'images/[^)]*' lessons/*.md | sort -u | while read p; do [ -f "$p" ] || echo "MISSING: $p"; done

# odd number of $ on a line (unclosed inline maths)
awk '{n=gsub(/\$/,"$")} n%2 {print FILENAME":"FNR}' lessons/*.md
```
