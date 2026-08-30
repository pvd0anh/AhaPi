# Authoring guide

How to publish a lesson. No programming required — everything happens in a form and on the GitHub
website.

> The editor's interface is in Vietnamese, because the site is. Button names below are quoted exactly
> as they appear on screen, so you can match them by eye.

## A. One-time setup

| Step | What to do |
|---|---|
| 1 | Create an account at **github.com** |
| 2 | Click **New repository** → name it `AhaPi` → choose **Public** → **Create** |
| 3 | Click **uploading an existing file** → drag in **the whole** project folder → **Commit changes** |
| 4 | Go to **Settings → Pages** → under *Branch* pick `main` / `(root)` → **Save** |
| 5 | Wait two minutes. Your site is at `https://<your-account>.github.io/AhaPi/` |

Send that address to your student. Done.

## B. Adding a lesson

Open **editor.html** from your computer by double-clicking it. You fill in the left-hand side; the
right-hand side previews the result as you type.

| Step | What to do | Where |
|---|---|---|
| 1 | Fill in *Ngày dạy*, *Chương*, *Tên bài*, and paste the YouTube link | editor.html |
| 2 | Write the theory (one language or two — see §C2), add vocabulary, add the exercises and their answers | editor.html |
| 3 | Click **⬇ Tải file .md** → the file downloads to your computer | editor.html |
| 4 | On GitHub, open the `lessons` folder → **Add file → Upload files** → drop the file in → **Commit** | GitHub |
| 5 | Open `index.json` on GitHub → click ✏️ → select all → paste it into the *Bước 6* box in the editor | Both |
| 6 | Click **Thêm bài này vào danh sách** → **Chép kết quả** → paste over `index.json` on GitHub → **Commit** | Both |

New lessons are appended to the **end** of the list. To place one elsewhere in a chapter, cut that
line and paste it higher up — the order of lines in `index.json` is the order the lessons are taught.

The lesson appears on the site after one or two minutes.

> **Why are steps 5–6 necessary?** The site has no way of knowing you uploaded a file. `index.json`
> is the table of contents: a lesson listed there is a lesson that shows up in the menu.

## C. Writing the content

| You type | You get |
|---|---|
| `## Công thức` | Section heading |
| `### Ví dụ` | Sub-heading |
| `**quan trọng**` | **quan trọng** (bold, in the accent colour) |
| `- mục a` | Bullet point |
| `> ghi nhớ` | Callout box |
| `$x^2 + 1$` | Formula inside the line |
| `$$\int x\,dx$$` | Formula on its own centred line |

Formulas you will reach for often: `\frac{a}{b}` fraction · `\sqrt{x}` square root · `x^{2}` power ·
`x_{1}` subscript · `\int` integral · `\sum` sum · `\Rightarrow` implies · `\leq` less than or equal.

**What if a formula is wrong?** The broken part shows as underlined red text in the preview pane.
Fix it before you download the file.

## C1. Adding a picture

| Step | What to do |
|---|---|
| 1 | On GitHub, open the **images** folder → **Add file → Upload files** → drop the picture in → **Commit** |
| 2 | In the editor, put the cursor where the picture should go and click **🖼 Chèn ảnh** |
| 3 | Replace `ten-file.png` with the real filename, and the text in square brackets with your caption |

```
![Sơ đồ tỉ số 2 : 3](images/so-do-ti-so.png)
```

The text in square brackets becomes a small caption directly under the picture. For no caption, leave
it empty: `![](images/so-do-ti-so.png)`.

Any size works — pictures are scaled down to fit automatically. Name files in **lowercase letters
with no accents and no spaces**; use hyphens instead of spaces.

> The preview pane will show a broken image, because your computer does not have that file yet.
> That is normal — it renders correctly once the site is live.

## C2. Bilingual lessons — English on one side, Vietnamese on the other

A lesson can put **both languages side by side**: English in the left column, Vietnamese in the
right, point for point.

In the *Bước 2* box, place the cursor where you want the block and click **⇄ Chèn khối song ngữ**.
The editor inserts the two markers for you:

```
[[EN]]
## Order Matters

A ratio is not just a pair of numbers — the **order** carries meaning.
[[VI]]
## Thứ tự có ý nghĩa

Tỉ số không chỉ là một cặp số — **thứ tự** mang ý nghĩa riêng.
```

| Marker | Meaning |
|---|---|
| `[[EN]]` | what follows goes in the **left column** |
| `[[VI]]` | the translation of that block, in the **right column** |
| `[[SHARED]]` | content that needs no translation — a formula, a picture, a table of numbers — runs the full width |

**Split it point by point.** Each section, each paragraph is its own block. Put the whole lesson in
one block and the two columns end up different lengths, drifting further apart the further you read.

A block you have written in English but not yet translated is fine — it simply runs full width like
an ordinary lesson. You can translate it later.

The *Tên bài tiếng Việt* field in *Bước 1* renders as a smaller line under the lesson title. Each
exercise also has its own Vietnamese fields, for the question and for the answer.

**What does the student see?** Any lesson with at least one bilingual block gets a picker:
**English · Cả hai · Tiếng Việt**.

| Choice | Result |
|---|---|
| Cả hai | Two columns side by side (stacked on a phone) |
| English | English only; each block has a **👁 Click to Translate** button that reveals the translation |
| Tiếng Việt | Vietnamese only; each block has a button to see the English again |

The student's browser remembers that choice across every lesson until they change it.

## C3. Writing vocabulary

In the *Bước 3* box, one word per line:

```
ratio /ˈreɪʃiəʊ/ : tỉ số
highest common factor : ước chung lớn nhất
```

English on the left of the `:`, Vietnamese on the right. The pronunciation between the two slashes is
**optional** — look it up on Oxford and paste it in if you want it.

The Vietnamese meaning is **hidden by default**: the student sees the English word and its
pronunciation, and clicks **👁 Click to Translate** to reveal the meaning. Choosing **Tiếng Việt** at
the top of the lesson reveals them all at once.

The student clicks 🔊 to hear the word, and the ↗ arrow to open that entry in the Oxford dictionary.
The voice comes from the student's own device, so it needs no network and nothing to install. On a
device with no English voice available, the speaker button hides itself.

## D. Editing or removing a lesson

| Task | How |
|---|---|
| Edit the content | Open the file in `lessons` on GitHub → click ✏️ → edit → Commit |
| Rename a lesson | Edit the matching line in `index.json` |
| Remove a lesson | Delete that lesson's line from `index.json` (you may leave the `.md` file in place) |
| Reorder lessons | Move the line up or down in `index.json` |
| Change the site's accent colour | Edit the `--accent:` line at the top of `index.html` |

## E. Things worth knowing

- Double-clicking `index.html` on your computer **will not work** — the browser refuses to read the
  files. View it through the GitHub Pages address instead. `editor.html` does work locally.
- A network connection is needed to display formulas and video.
- When a student clicks **Đánh dấu đã học**, the ✓ is saved on that student's own device and is sent
  nowhere. Clearing their browser history erases it.
- A lesson's `id` is its `.md` filename. The two must match — the editor takes care of that for you.
