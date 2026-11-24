# AhaPi

Static site publishing maths lessons from the [Thảo Lê](https://www.youtube.com/@ThaoLe-pm8mh)
YouTube channel: video, Markdown + LaTeX theory, vocabulary with pronunciation, and exercises with
hidden answers. Also hosts standalone interactive geometry models. No server, database, login or
build step.

**Live:** https://thaole.io.vn

## Structure

| Path | Role |
|---|---|
| `index.html` | Home page: lesson card, interactive-model cards, editor link |
| `lessons.html` | Lesson app. Reads `index.json`, fetches `lessons/<id>.md` on demand |
| `editor.html` | Lesson editor with live preview; runs from `file://` |
| `index.json` | Table of contents. Row order = teaching order |
| `lessons/*.md` | One lesson per file; filename = `id` |
| `images/` | Pictures used in lessons |
| `models/*.html` | Interactive pages (side-angle-side model, equal-triangle exercise, X-calliper) |
| `models/models.json` | Home-page cards for the interactive pages. Array order = card order |
| `models/art/` | Card pictures |
| `.nojekyll`, `CNAME` | GitHub Pages config — keep both |
| `AUTHORING.md` | Click-by-click guide for the teacher |
| `AGENTS.md` | Invariants and conventions for contributors and coding agents |

Rendering uses KaTeX 0.16.9 and marked 11.1.1 from jsDelivr. UI text is Vietnamese; code and docs
are English.

## Run locally

```bash
python3 -m http.server 8000   # http://localhost:8000/
```

`lessons.html` needs http(s) because it uses `fetch`. `editor.html` opens directly from disk.

## Lesson format

```markdown
[[VIDEO]] https://youtu.be/xxxxxxxxxxx
[[TITLE VI]] Vietnamese title

## Heading

Text with inline maths $a^2+b^2=c^2$ and display maths:

$$\frac{a}{b}$$

> **Ghi nhớ:** a callout

![Caption](images/diagram.png)

[[VOCABULARY]]
ratio /ˈreɪʃiəʊ/ : tỉ số

[[EXERCISES]]

[[Q]] Question
[[A]] Answer, hidden until clicked
```

- All tags are optional. `[[VIDEO]]` and `[[TITLE VI]]` sit alone on their line; `[[VOCABULARY]]`
  must precede `[[EXERCISES]]`.
- Vocabulary lines are `word /ipa/ : meaning` (IPA optional). Each gets a speaker button (browser
  speech synthesis) and an Oxford Learner's Dictionaries link.
- Image alt text becomes the caption; paths resolve from the site root.

### Bilingual lessons

```markdown
[[EN]]
A ratio compares two quantities.
[[VI]]
Tỉ số so sánh hai đại lượng.

[[SHARED]]
$$a : b = \frac{a}{b}$$

[[Q]] Write the ratio. [[VI]] Hãy viết tỉ số.
[[A]] $5:8$ [[VI]] $5:8$
```

`[[EN]]`/`[[VI]]` pairs render side by side, kept level per row; `[[SHARED]]` and untranslated blocks
span full width. Lessons with a pair or an exercise translation get a switch
(**English · Cả hai · Tiếng Việt**), remembered in `localStorage`. Columns stack below 900px.

## Adding content

- **Lesson**: fill in `editor.html`, download the `.md` into `lessons/`, paste the current
  `index.json` into the editor to get an updated copy, commit both. Editing a lesson touches only
  its `.md`; reordering or removing touches only `index.json`.
- **Interactive page**: add a self-contained `.html` to `models/` (prefix relative paths with
  `../`, optionally copy the `.home-link` block from `models/cgc-model.html`) and an entry to
  `models/models.json`:

  | Field | Required | Meaning |
  |---|---|---|
  | `file` | yes | filename in `models/`; ASCII letters, digits, `-` `_` `.`, ends in `.html` |
  | `title` | yes | card heading |
  | `text` | no | one-line description |
  | `tag` | no | label above the heading, e.g. `Toán 8` |
  | `new` | no | `true` fills the tag with the accent colour |
  | `image` | no | path relative to `models/`, e.g. `art/x.svg`; omitted → generic drawing |
  | `kind`, `go` | no | footer texts; defaults `Mô hình tương tác` / `Khám phá →` |

  Invalid JSON or a bad entry shows a readable notice on the home page instead of breaking it.

`AUTHORING.md` covers both flows for non-developers.

## Deploy

Push to `main`; GitHub Pages serves the repo root. Netlify or Vercel also work with no configuration.

## Customising

- Accent colour: `--accent` in `index.html`, `lessons.html`, `editor.html`, plus the hardcoded
  `#5B3FA8` in `models/*.html`, `404.html`, `favicon.svg` and `index.html`'s default card art.
- Lesson paper background: `--paper`, `--grid-line`, `--grid-size` in `lessons.html`.

## Limits

- Progress is per browser (`localStorage`); no sync, no teacher view.
- KaTeX, marked and fonts come from CDNs, so the site needs a connection.
- Private YouTube videos cannot be embedded.
