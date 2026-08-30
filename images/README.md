# Lesson images

Drop image files into this folder, then reference them from a lesson with ordinary
Markdown image syntax:

    ![Caption text](images/file-name.png)

The alt text renders as a caption underneath the picture. Leave the square brackets
empty for no caption:

    ![](images/file-name.png)

Paths resolve from the site root, so `images/…` is what you want. A full external URL
works too.

## Conventions

- Name files in lowercase ASCII with hyphens — no accents, no spaces.
- Any size works; images are scaled down to fit the column automatically.
- Prefer PNG for diagrams and JPEG for photographs.
