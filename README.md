# Personal site (Hugo → Cloudflare Pages)

Black background, white text, IBM Plex Mono. Layout follows the WordPress
**Indice** theme: sticky index on the left, one text column on the right.
Everything you write is a Markdown file in `content/`.

```
content/
  articles/  projects/                            ← text entries (one .md file each)
  writings/                                       ← one flat section: every piece is a file or folder here
  paintings/  juvenilia/                          ← thumbnail grids (one folder per piece)
  demos/                                          ← entries with an audio player
  resume.md                                       ← plain page
```

Sidebar order (`hugo.toml`): Home, Articles, Writings, Paintings, Demos, Juvenilia, Projects, Resume.

## Run it locally

Install [Hugo](https://gohugo.io/installation/) (extended edition, v0.168.0 was used to build this), then:

```sh
hugo server -D        # http://localhost:1313 — -D also shows drafts
```

## Add things

```sh
hugo new content writings/my-piece.md
hugo new content articles/my-article.md
hugo new content paintings/my-painting     # folder; drop cover.jpg inside it
hugo new content juvenilia/my-comic        # folder; cover.jpg + page-01.jpg, page-02.jpg …
hugo new content demos/my-demo             # folder; drop demo.mp3 inside it
```

New files start with `draft: true`. Delete that line (or set `false`) to publish.
An entry dated in the future stays hidden until that date, so use today's date or earlier.
Delete the `sample-*` files and folders once you have your own, and edit `resume.md` (a PDF can go in `static/`).

- **Writings:** one flat section, no sub-sections. Say what a piece is (poem, lyrics, muse…) in its title or add tags. Every line break you type is kept, and a blank line starts a new stanza. For a prose piece add `verse: false` to its front matter. To show an image in a piece, put `{{< fit src="pic.jpg" caption="…" >}}` where you want it (the entry must be a folder with `index.md` and the image).
- **Images:** in Articles and Paintings every image is shown whole at the column width (height capped at 70% of the screen) and opens in a pop-up when clicked. Juvenilia is unaffected.
- **Nesting:** any folder with an `_index.md` (give it a `title:`) becomes a section, and you can nest as deep as you like. Entries (and nested list pages) end with a "← back to …" link to their parent, and the sidebar highlights the top-level section you are in, so you can always climb back up. Use `weight:` in `_index.md` to order sub-sections.
- **Light / dark:** the small circle at the top right switches modes and remembers your choice. The site starts dark. Colours live at the top of `assets/css/main.css`.
- **Short front-page preview:** put `<!--more-->` on its own line in a long entry; the home page and
  section lists stop there and show "continue →". Without it, the whole entry is shown.
- **Juvenilia pages / extra painting images:** keep `{{< gallery >}}` in the text. It shows every image in
  that folder except `cover.*`, in filename order. Add `hide_cover: true` to the front matter to hide
  the cover on the entry's own page.
- **Audio anywhere:** `{{< audio src="file.mp3" >}}`, or `audio: "https://…"` in the front matter of a demo.
- **Tags:** `tags: ["rain", "night"]` in front matter; they appear under the entry and link to a tag page.
- **Sidebar menu, site name, tagline:** `hugo.toml`.

## Deploy on Cloudflare Pages

1. Put this folder in a GitHub (or GitLab) repo and push.
2. Cloudflare dashboard → **Workers & Pages → Create → Pages → Connect to Git**, pick the repo.
3. Framework preset **Hugo** (or: build command `hugo --gc --minify`, output directory `public`).
4. Add an environment variable `HUGO_VERSION` = `0.168.0` (Cloudflare defaults to an older Hugo).
5. Deploy. Then set `baseURL` in `hugo.toml` to your real address (`https://<project>.pages.dev/` or your own domain) and push again.

Every `git push` redeploys.

## Images and music

- Keep images under ~2 MB and audio as MP3 (128–192 kbps). Pages caps single files at 25 MiB.
- Hugo resizes images at build time, so you can commit full-size photos without slowing the site.
- If the audio archive grows large, upload to **Cloudflare R2** and use the public URL in `audio:` instead of committing files.

## Credits

- Layout and palette: [Indice](https://wordpress.com/theme/indice) by Automattic, GPL-2.0-or-later.
- Font: [IBM Plex Mono](https://github.com/IBM/plex), SIL Open Font License 1.1 (files in `static/fonts/`).
# blog
