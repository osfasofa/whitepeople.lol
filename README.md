# whitepeople.lol

Sister site to [blackpeople.lol](https://blackpeople.lol). Same codebase, light
theme, and it is only ever Rick Astley.

```bash
npm install
npm run dev      # http://localhost:4321
```

Node 22+ is required (Astro 7). Pinned via `.mise.toml`.

## What differs from blackpeople.lol

The two repos are otherwise identical copies. If you fix something in the feed
itself, the change has to be applied in both places by hand.

| | blackpeople.lol | whitepeople.lol |
| --- | --- | --- |
| Palette | black bg, white text | white bg, dark text |
| giscus theme | `dark` | `light` |
| `theme-color` | `#000000` | `#ffffff` |
| Content | whatever's good | Rick Astley |

Everything colour-related lives in the `:root` token block at the top of
`src/styles/global.css`, plus the drawer surfaces further down. No logic differs.

---

## Publishing a new post

1. Create a file in `src/content/posts/`. The filename becomes the slug:

   ```
   src/content/posts/song-name.md
   ```

2. Fill in the frontmatter:

   ```markdown
   ---
   title: 'Rick Astley — Song Name'
   youtube: dQw4w9WgXcQ
   date: 2026-08-09T14:30:00-04:00
   blurb: 'Optional one-liner.'
   ---
   ```

   **Only `youtube` is required.** This is a complete, valid post:

   ```markdown
   ---
   youtube: dQw4w9WgXcQ
   ---
   ```

   | Field    | Required | Notes                                                       |
   | -------- | -------- | ----------------------------------------------------------- |
   | `youtube`| **yes**  | The **video ID only** — the bit after `v=`, not the full URL. |
   | `title`  | no       | Omit it and no title renders. Nothing is fetched from YouTube.|
   | `date`   | no       | Date, or date **and time**. Omit it and the post goes on top. |
   | `blurb`  | no       | Small line under the title. Works with or without a title.    |

   If a post has neither a title nor a blurb, the overlay gradient doesn't render
   at all — you get bare video, not an empty smudge along the bottom.

   Include an offset when you give a time (`T14:30:00-04:00`); a bare time is read
   as UTC and can shift a post onto the wrong day. Undated posts sort above
   everything dated, and ties fall back to alphabetical order by filename.

3. Commit and push. Vercel rebuilds automatically.

The markdown body is ignored — only frontmatter is read. The filename is also
the comment thread key, so **renaming a file orphans its comments**.

---

## Comment setup (not done yet)

Comments use [giscus](https://giscus.app), backed by GitHub Discussions. The
code is wired up but **`repoId` and `categoryId` in `src/config.ts` are still
empty**, so the drawer currently opens and shows a "not configured yet" note
instead of a thread.

To finish it:

1. Make this repo **public** — giscus can't read private repos.
2. **Settings → General → Features →** tick **Discussions**.
3. Install the giscus app on this repo: <https://github.com/apps/giscus>.
4. Open <https://giscus.app>, enter `osfasofa/whitepeople.com`, choose the
   **"Discussion title contains a specific term"** mapping and the
   **Announcements** category.
5. Copy `data-repo-id` and `data-category-id` from the generated snippet into
   `src/config.ts`, then commit.

Don't copy giscus.app's `data-term` (the site sets a per-post term at runtime)
or its `data-strict="0"` — we keep strict matching at `1` so one slug can't
attach to a discussion that merely contains it.

These IDs are not secrets; giscus puts them in client-side HTML by design.

---

## Deploying

Import the repo on [Vercel](https://vercel.com/new) — Astro is auto-detected, no
adapter needed. Then **Settings → Domains → Add Domain → `whitepeople.lol`**,
and put the A record and CNAME target it shows into GoDaddy.

The domain currently sits on GoDaddy nameservers with parked A records, so
you'll need to **delete both existing `@` A records** before adding Vercel's,
and check the separate **Forwarding** section is off.

---

## Layout

```
src/
  config.ts              site + giscus + overlay settings
  content.config.ts      post frontmatter schema
  content/posts/*.md     one file per song
  components/
    VideoSection.astro   one full-screen video
    CommentDrawer.astro  the slide-up panel (one, reused)
  scripts/
    player.ts            IFrame API, IntersectionObserver, sound
    comments.ts          drawer + lazy giscus mounting
  styles/global.css
  pages/index.astro
```
