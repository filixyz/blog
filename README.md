# Personal site (Hugo → Cloudflare Pages)

Dark by default (`light/dark` text top right), Inter type, and an optional purple text colour (small circle at the foot of every page). Layout follows the WordPress
**Indice** theme: sticky index on the left, one text column on the right.
Everything you write is a Markdown file in `content/`.

```text
content/
  resume.md                                       ← plain page
  projects/                                       ← software / engineering / science write-ups (.md files)
  notebook/
    documents/                                    ← .md files (or folders with images)
    writings/                                     ← poems, lyrics, muses… one flat section
  collection/
    paintings/                                    ← thumbnail grid (one folder per piece)
    juvenilia/                                    ← an ordinary list of posts (comics are posts with images)
    songs/                                        ← entries with an audio player
```

Click the site name for the chronological feed of everything except the resume.

**The sidebar is plain links** (`hugo.toml`, `[[menus.main]]`): Resume, Projects, Notebook, Collection. The section you are in, or the
one that contains the page you are on, is underlined. There is no folder expansion any more, on desktop or phones. Notebook and Collection
pages show a small row of their subsections (documents, writings / paintings, songs, juvenilia) above the posts. The whole nested tree
(every section, subsection and entry) lives on the Browse page under `tree`.

A breadcrumb trail (`Notebook / Writings / my piece`) sits above every page that has parents.

## Run it locally

Install [Hugo](https://gohugo.io/installation/) (extended edition, v0.168.0 was used to build this), then:

```sh
hugo server -D        # http://localhost:1313 — -D also shows drafts
```

## Add things

```sh
hugo new content notebook/writings/my-piece.md
hugo new content notebook/documents/my-document.md
hugo new content projects/my-project.md
hugo new content collection/paintings/my-painting -k paintings   # folder; drop cover.jpg inside it
hugo new content collection/juvenilia/my-comic -k juvenilia      # folder; cover.jpg + page-01.jpg, page-02.jpg …
hugo new content collection/songs/my-song -k songs               # folder; drop demo.mp3 inside it
```

New files start with `draft: true`. Delete that line (or set `false`) to publish.
An entry dated in the future stays hidden until that date, so use today's date or earlier.
Delete the `sample-*` files and folders once you have your own, and edit `resume.md` (a PDF can go in `static/`).

- **Writings:** one flat section, no sub-sections. Say what a piece is (poem, lyrics, muse…) in its title or add tags. Every line break you type is kept, and a blank line starts a new stanza. For a prose piece add `verse: false` to its front matter. To show an image in a piece, put `{{< fit src="pic.jpg" caption="…" >}}` where you want it (the entry must be a folder with `index.md` and the image).
- **Every post looks the same** (feed and its own page; only the resume differs): title, a small `expand` toggle under it on the right, the body, then date and tags. Images anywhere in a post (cover, Markdown images, `{{< gallery >}}`) sit left-aligned, whole, in one capped 3:2 box (`--box-w` / `--box-h` at the top of `assets/css/main.css`) and open in a pop-up when clicked. No shortcode is needed any more.
- **Expand / compact:** `expand` (chevron down) makes that post's images fill the width of the text column and stack downwards, for reading comics; the label becomes `compact` (chevron left). Each post has its own toggle. Nothing is remembered: refresh, the back button, or leaving and coming back all return to compact. If a post has no images and no cut-off text, the toggle does nothing and the label does not change. Without JavaScript the toggle is hidden.
- **Long posts in the feed:** a post over `feedWords` words (`hugo.toml`, default 250) is cut short with a fade and `continue →` (to its own page) while compact; `expand` shows it whole in place. A post's own page always shows the full text; there `expand` only affects images. A `<!--more-->` you place yourself wins over the word limit.
- **Nesting:** any folder with an `_index.md` (give it a `title:`) becomes a section. Entries end with a "← back to …" link, and the sidebar and breadcrumbs show where you are. To make a new top-level group, add its folder and `[[menus.main]]` entries (with `parent = "…"` for children) in `hugo.toml`. Give a section's `_index.md` `cascade: type: <name>` so its entries share a type.
- **Light / dark:** the `light/dark` text at the top right switches modes and remembers your choice. The site starts dark. Colours live at the top of `assets/css/main.css`.
- **Short front-page preview:** put `<!--more-->` on its own line in a long entry; the feed and section lists stop there and show "continue →" while compact (expand shows everything). Without it, only posts over `feedWords` are cut.
- **Comics / extra images:** keep `{{< gallery >}}` in the text. It shows every image in that folder except `cover.*`, in filename order, as normal post images (capped; full width when expanded). Add `hide_cover: true` to the front matter to hide
  the cover on the entry's own page.
- **Audio anywhere:** `{{< audio src="file.mp3" >}}`, or `audio: "https://…"` in the front matter of a song. The player is a slim custom one (`play`/`pause`, a thin line you can click or use arrow keys on, time); without JavaScript the browser's own controls appear. Its look is the `.player` rules in `main.css`, its script is in `partials/post-js.html`.
- **Tags:** `tags: ["rain", "night"]` in front matter; they appear under the entry and link to a tag page.
- **Sidebar menu, site name, tagline:** `hugo.toml`.

## Snippets (copy and edit)

Front matter at the top of any entry:

```yaml
---
title: "My piece"
date: 2026-10-17
tags: ["rain", "night"]
verse: false          # Writings only: set false for prose, so lines re-wrap normally
---
```

Shortcodes inside an entry:

```go-html-template
{{< fit src="pic.jpg" caption="Optional caption" >}}
{{< audio src="demo.mp3" >}}
{{< gallery >}}
```

Another child under Notebook or Collection, in `hugo.toml`:

```toml
[[menus.main]]
  name = "Essays"
  pageRef = "/notebook/essays"
  parent = "Notebook"
  weight = 33
```

and in `content/notebook/essays/_index.md`:

```yaml
---
title: "Essays"
cascade:
  type: essays
---
```

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

## Troubleshooting

- **My new entry does not appear:** it still has `draft: true`, or its `date:` is in the future. Remove the draft line and use today's date or earlier.
- **A section is empty after I added a folder:** the folder needs an `_index.md` with a `title:`.
- **Sidebar does not show my new section:** add it to the menu in `hugo.toml` (see the snippet above).
- **Line breaks in a poem vanish:** the piece must live under `content/notebook/writings/`, or have `verse: true`.
- **Cloudflare build fails with a Hugo version error:** set `HUGO_VERSION` to `0.168.0` (or newer) in the project's environment variables.
- **Images look blurry in the pop-up:** the pop-up shows the original file, so commit a larger original.

## Header and mobile menu

The site name (`title` in `hugo.toml`, currently `felix corpus`) links to the chronological feed; the red `(feed)` beside it says so.
The top-right text is the theme switch and shows only the mode you will switch to (dark is the default).
On phones the header is the name on the left, then `menu`, then the theme switch, both on the right. `menu` opens the section links as a full-screen overlay; the arrow
at its top right (or Esc) closes it. Without JavaScript the tree sits under the header instead. Desktop keeps the sidebar.

## Browse page

`browse` in the header (left of the theme switch; on phones between `menu` and the theme switch) opens `/browse/`:
two options, `search | tree` (search is the default; the other is greyed), then a search box, a `browse by date` list (month, count) and every entry, newest first, with its date. `back to feed` returns to the feed.
On desktop, picking a month shows only that month and replaces the list with `back to all dates`. On phones the dates are a capped, scrollable dropdown with `all dates` selected by default; picking a month filters the list and the dropdown shows your choice. Search matches words in titles and text
(all words must match) and combines with the month filter. It needs JavaScript for searching and filtering; without it the page is
the full list and the month links jump down to that month. The search index is `/browse/index.json`, built from your entries on each
deploy, so there is nothing to maintain. `tree` swaps the page for a drill-down view: it starts at the top level (Resume, Projects, Notebook, Collection); clicking a folder's **name** (folders are blue, `--folder` at the top of `main.css`; files keep the text colour) enters it; the small chevron beside it only peeks inside in place, showing that folder's own subfolders and posts (one level, nothing nested further) without moving the root or the breadcrumb; listing its subfolders then its entries, under a clickable breadcrumb (`tree / Collection / Songs`). `← parent` goes up one level and `open page` goes to that section's own page. Nothing is truncated and nothing scrolls sideways at any depth; nothing is remembered. Without JavaScript the tabs are hidden and the page is the plain full list. The page is `content/browse/_index.md`; the resume is left out of search, as in the feed.

Dates on posts read like `February 10, 2023`. To change that, edit the format string `"January 2, 2006"` in `layouts/partials/post.html` and `layouts/browse/list.html`.

## Banner image

Drop your image in **`assets/`** (not `static/`) and name it in `hugo.toml`: `header = "header.jpg"`. Wide 4:1 images (1600 x 400 px is ideal) work best; other shapes are cropped to 4:1 from the centre. On desktop it sits in the
sidebar and bleeds off the left screen edge, with a gutter before the blog text; on phones it sits between the header row and the page, edge to edge. `header = ""` hides it.

**Changing it as often as you like costs nothing.** On every build Hugo makes 600, 1200 and 1600 px copies in WebP and JPEG, and the browser downloads only the one that fits its screen
(roughly 3 to 30 KB instead of one big file). Each copy's file name contains a hash of the picture (`/banner/header_hu_<hash>.webp`), and `static/_headers` tells Cloudflare and browsers to
keep everything under `/banner/` for a year without re-checking. Replace the image and the hash, hence the URL, changes, so visitors get the new banner on their next visit while repeat page views
never re-request or revalidate it. No renaming or cache clearing is needed. The banner loads with high priority (not lazily), so it appears with the first screen.

## Typeface

`font = "inter"` (default), `font = "wikipedia"`, `font = "typewriter"` or `font = "mono"` under `[params]` in `hugo.toml`. Typewriter asks for **American Typewriter**,
which is Apple's licensed font and cannot be bundled: Macs and iPhones show the real one, everyone else gets **Arvo**
(free, OFL, in `static/fonts/`). Code blocks stay in IBM Plex Mono either way.

`mono` uses IBM Plex Mono (bundled, same as code blocks) for all text.

`wikipedia` copies Wikipedia's stacks: body text in the visitor's system `sans-serif`, titles and headings in
`"Linux Libertine", Georgia, Times, serif`. Nothing is bundled, so what shows depends on the device (as on Wikipedia itself).

## Purple text mode (change it with your mood)

Text is plain by default: grey on near-black in dark mode, black on white in light mode. **Purple mode** switches the
lettering to the blue/lavender colours (light: blue on off-white, dark: lighter blue on near-black). The code
colours and images are not affected.

- **Visitors** switch it with the tiny circle at the bottom right of every page: empty circle with an X = plain,
  filled circle = purple. Their choice is remembered in their browser.
- **You** set the site-wide default in `hugo.toml`, under `[params]`:

```toml
purple = false   # plain colours for everyone (default)
# purple = true  # purple mode for everyone
```

  Change it, commit, push. When the default changes, everybody (including you) gets the new default, even people
  who clicked the circle before; they can click it again to override.
- **The colours themselves** are the two `data-purple="on"` lines near the top of `assets/css/main.css`
  (one for dark, one for light). Edit the hex values there to change the purple.

## Code blocks

**Switch colouring off:** in `hugo.toml` set `syntax = false` under `[params]` (default `true`), then redeploy. Code then
shows in the plain text colour. It is a site-wide setting; visitors have no switch for it.

Fenced code is coloured like a GitHub README, using the language you put after the opening fence:

````markdown
```cpp
std::array<std::uint8_t, 68> out{};
```
````

Dark mode uses GitHub's dark palette and light mode the light one (`assets/css/syntax.css`, generated with
`hugo gen chromastyles --style=github` and `--style=github-dark`). Without a language the block stays plain.
Long lines scroll sideways. `inline code` stays plain.

## Changing the look

Everything visual is in `assets/css/main.css`; the knobs are at the top:

| Variable | Controls |
|---|---|
| `--base`, `--primary`, `--secondary` | Background, text and dimmed-text colours (one block per theme, plus the purple-mode blocks) |
| `--size` | Text size (desktop; a smaller value is set for phones near the bottom of the file) |
| `--pad` | Space between the text and the screen edge (the same in both themes) |
| `--box-w`, `--box-h` | The capped image box (3:2 by default) |

Templates are in `layouts/` (`partials/post.html` is the one post layout, `partials/sidebar.html` the section links, `partials/breadcrumbs.html` the trail).

## Credits

- Layout and palette: [Indice](https://wordpress.com/theme/indice) by Automattic, GPL-2.0-or-later.
- Fonts: [Inter](https://rsms.me/inter/) (text) and [IBM Plex Mono](https://github.com/IBM/plex) (code), both SIL Open Font License 1.1 (files in `static/fonts/`).
