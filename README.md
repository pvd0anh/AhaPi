# AhaPi

A static site for publishing maths lessons — YouTube video, LaTeX theory, and exercises with hidden answers. No server, no database, no login, no build step.

**Live:** https://pvd0anh.github.io/AhaPi/

## How it works

`index.html` reads `index.json` (the table of contents) and fetches each lesson from `lessons/<id>.md` on demand. Formulas render with KaTeX, Markdown with marked. That is the whole architecture — two HTML files and a folder of text.

| Path | Role |
|---|---|
| `index.html` | The student-facing app |
| `soan-bai.html` | Lesson editor for the teacher — fill a form, download a `.md`, get the JSON line |
| `index.json` | Table of contents. **Row order = teaching order** |
| `lessons/*.md` | One file per lesson. Filename must match its `id` |
| `images/` | Pictures used inside lessons |
| `.nojekyll` | **Keep this.** Without it GitHub Pages runs Jekyll and the `.md` lessons stop loading |
| `HUONG-DAN.md` | Vietnamese step-by-step guide, written for a non-technical teacher |

## Content

23 lessons across 5 chapters, built from the [Thảo Lê](https://www.youtube.com/@ThaoLe-pm8mh) YouTube channel:

| Chapter | Lessons |
|---|---|
| Ratio | 8 |
| Decimal | 6 |
| Proportional Relationship | 2 |
| Toán lớp 7 (Vietnamese) | 6 |
| Better Everyday | 1 |

## Lesson format

```markdown
[[VIDEO]] https://youtu.be/xxxxxxxxxxx
[[TÊN VI]] Tên bài bằng tiếng Việt

## A heading

Plain text, **bold**, inline maths $a^2+b^2=c^2$.

$$\int_{0}^{1} x\,dx = \frac{1}{2}$$

> A callout box

![Caption shown under the picture](images/diagram.png)

[[TỪ VỰNG]]
ratio /ˈreɪʃiəʊ/ : tỉ số
highest common factor : ước chung lớn nhất

[[BÀI TẬP]]

[[Câu]] Question text
[[Đáp án]] Answer, hidden until the student clicks
```

Every tag is optional. `[[VIDEO]]` and `[[TÊN VI]]` must sit alone on their own line; everything after `[[BÀI TẬP]]` is parsed as exercises.

Images use ordinary Markdown, `![caption](images/file.png)`. Paths resolve from the site root, so `images/…` is what you want; a full external URL works too. The alt text becomes a caption under the picture — leave the brackets empty for no caption.

Each `[[TỪ VỰNG]]` line is `english /ipa/ : vietnamese`, with the IPA optional. Every entry gets a speaker button — pronunciation comes from the browser's own speech engine, so it needs no files, no API key and no network — plus a link out to Oxford Learner's Dictionaries for the full entry.

## Bilingual lessons

The audience reads Vietnamese and studies maths in English, so a lesson can carry both languages side by side. Mark up the theory in pairs — `[[EN]]` opens the English side of a block, `[[VI]]` its translation:

```markdown
[[EN]]
## Order Matters

A ratio is not just a pair of numbers — the **order** carries meaning.
[[VI]]
## Thứ tự có ý nghĩa

Tỉ số không chỉ là một cặp số — **thứ tự** mang ý nghĩa riêng.

[[CHUNG]]

$$a : b = \frac{a}{b}$$

[[Câu]] A box contains 5 red pens and 8 blue pens. Write the ratio.
[[VI]] Một hộp có 5 cái bút đỏ và 8 cái bút xanh. Hãy viết tỉ số.
[[Đáp án]] $5:8$
[[VI]] $5:8$
```

Each pair renders as one grid row, so the two languages stay level with each other no matter how the translation runs long. `[[CHUNG]]` marks a block that needs no translation — a formula, a picture, a table of numbers — and it spans the full width. A block left untranslated (an `[[EN]]` with no `[[VI]]`) does the same, so a half-translated lesson still reads correctly.

A lesson containing at least one pair gets a display switch: **English · Cả hai · Tiếng Việt**. In the two single-language modes each block keeps a *Click to Translate* button that reveals its counterpart in place; the choice is remembered in `localStorage` and applies to every lesson. Below 900px the columns stack, each translation labelled and rule-marked rather than side by side.

Vocabulary meanings are hidden behind the same click — the English word, its IPA and its speaker button show, the Vietnamese waits until asked. Switching to **Tiếng Việt** reveals them all at once.

Exercises take `[[VI]]` inline the same way, on the question and on the answer.

## Adding a lesson

Open `soan-bai.html` by double-clicking it — it runs locally with a live preview. Fill in the form, download the `.md` into `lessons/`, then paste your current `index.json` into step 6 to get an updated copy. Commit both files; GitHub Pages redeploys itself in about a minute.

Editing an existing lesson only touches its `.md`. Renaming, reordering or removing a lesson only touches `index.json`. Adding a new one needs both — a lesson missing from `index.json` will not appear in the menu.

## Deploy

Push to a public GitHub repo, then **Settings → Pages → Branch: `main` / `(root)`**. Netlify and Vercel also work: drag the folder in, no build configuration needed.

## Customising

The accent colour is the `--nhan:` variable at the top of `index.html`; the ruled-paper backdrop behind the theory of each lesson is `--giay` (paper), `--o-ly` (grid line) and `--o` (cell size) next to it. The name appears in `<title>`, the header, `document.title`, `soan-bai.html` and `404.html`; the `π` logo lives in `index.html`, `404.html` and `favicon.svg`.

## Known limits

- Progress is stored per browser in `localStorage`. It does not sync across devices, and the teacher cannot see who has studied what.
- `index.html` will not run from `file://` — browsers block `fetch` there. Use GitHub Pages or any static server. (`soan-bai.html` does run locally.)
- KaTeX, marked and Google Fonts load from CDNs, so formulas and video need a connection. Vendoring them locally is the fix if you need full offline use.
- Videos set to Private on YouTube cannot be embedded.
