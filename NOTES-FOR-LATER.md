# Notes for later

This file is just for you. It is not part of the website and is never published.

---

## How this website works

There are **two** repositories, and they do different jobs:

| Repository | What it is |
|---|---|
| `starter-hugo-academic` | **The source. This is the one you edit.** |
| `upasanasen.github.io` | The finished website. Generated automatically. **Never edit by hand** — it gets overwritten. |

When the deploy is working, pushing a change to the source repo makes a GitHub
robot rebuild the site and publish it.

## To preview the site on your laptop

```
cd ~/Documents/GitHub/starter-hugo-academic
hugo server
```

Then open http://localhost:1313 in a browser. Edits show up instantly.
Press `Ctrl+C` in the Terminal to stop it.

## Where things live

| What you want to change | File |
|---|---|
| Your bio, job title, photo, social links | `content/authors/admin/_index.md` |
| Which sections appear on the homepage | `content/_index.md` |
| The top navigation menu | `config/_default/menus.yaml` |
| Blog posts | `content/post/` — one folder per post |
| Projects | `content/project/` |
| Publications | `content/publication/` |

---

## Switched off, waiting for you

### 1. Gallery

Currently hidden because there are no photos yet.

**To turn it on:**

1. Put photos in `assets/media/albums/gallery/` — **`.jpg` or `.png` only**.
   Any other file type in that folder will break the build.
2. In `content/_index.md`, find the `GALLERY` comment block and remove the
   leading `# ` from the 8 lines beneath it.
3. In `config/_default/menus.yaml`, do the same for the Gallery entry.

Note: the gallery cannot be empty. It needs at least one image or the site
will not build.

### 2. Publications

Two publications are already written and ready:

- *Are Urban home-makers more educated in traditional herbal medicine than
  their rural counterpart?* (2015)
- *Harpadon nehereus: An Easily Accessible yet Untapped Cardiac Remedy in
  West Bengal* (2016)

They are hidden in four places. **To turn them on:**

1. In both files under `content/publication/`, change `draft: true` to
   `draft: false`.
2. Rename `content/publication/_index.md.bkp` back to `_index.md`.
3. In `content/_index.md`, find the `PUBLICATIONS` comment block and remove
   the leading `# ` from the lines beneath it.
4. In `config/_default/menus.yaml`, do the same for the Publications entry.

**Still needed before publishing them:** co-author names (currently listed as
Upasana Sen only), and exact dates — only the years are known, so both
currently show January. Links, abstracts and PDFs are optional.

---

## Two things still outstanding

### Deploy workflows are paused

They were disabled on 19 Sept 2026 so that removing the CV could not
accidentally publish a half-finished homepage. **Nothing you push will reach
the live site until they are switched back on.**

To re-enable:

```
gh workflow enable "Build and deploy site" --repo upasanasen/starter-hugo-academic
```

### The deploy is broken

Separately from being paused: the last run on 25 Aug 2026 reported success but
did not actually update the live site. This needs investigating before
publishing will work.

---

## CV removal (19 Sept 2026)

Your CV was publicly downloadable from four places. All were removed and both
repositories' histories were rewritten.

**One step may still be outstanding:** the request to GitHub Support asking them
to purge their cached copies. The message is saved at
`~/Documents/GitHub/github-support-request.md` — send it at
https://support.github.com/contact if you have not already.

Backups from before the rewrite are at
`~/Documents/GitHub/_backup-before-history-rewrite/` (85 MB). Safe to delete
once you are confident everything is fine.
