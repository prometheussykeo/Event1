# Conference Explorer

A searchable registry of conferences, summits and hackathons, with live
registration links and deadline flags.

**This folder is the whole website.** It is self-contained: no build step, no
dependencies, no server. Whatever host serves these files serves the site.

```
index.html      the page (CSS and JS inline, nothing external except web fonts)
events.json     the data it reads at load
vercel.json     security headers
```

`index.html` and `events.json` are generated from an Obsidian vault. Do not edit
them by hand; rebuild instead (see *Updating* below).

## Shipping it

The one rule: **`index.html` must end up at the top level of the repository.**
Everything else follows from that.

### Option A — push with git (recommended)

The folder is already a git repository with its files at the root, so a push puts
them at the repo root and nothing can nest:

```bash
git remote add origin https://github.com/<user>/<repo>.git
git push -u origin main
```

Create the GitHub repo **empty** — no README, no .gitignore — or the first push is
rejected as non-fast-forward.

### Option B — upload through the GitHub website

Drag in the **files themselves**, not the folder around them. Dragging the folder
creates `<folder>/index.html`, one level down, and the site will 404.

## Deploying to Vercel

Import the repository, then:

- **Framework Preset:** Other
- **Root Directory:** the folder that directly contains `index.html`
- **Build Command / Output Directory:** leave both empty

**Root Directory is the setting that bites.** Vercel serves that folder at `/`, so
it must be the one holding `index.html`. If the files did end up nested (the repo
shows `<repo> / site / index.html`), either set Root Directory to `site` or move the
files up. A wrong Root Directory produces `404 NOT_FOUND` on a deployment that
otherwise built perfectly green — the build is fine, there is simply nothing at the
path being served. It can be changed any time under **Settings -> Build and
Deployment**, followed by a redeploy.

Once connected, every push to the default branch redeploys automatically.

## Updating the data

The Obsidian vault is the source of truth, not this folder:

```bash
python "_system/scripts/explorer_ui_data.py"
python "_system/scripts/build_site.py"
git add -A && git commit -m "Refresh registry data" && git push
```

## What this site is not

It is the read-only shop window. The planner boxes (Tracking / Attending) and any
notes typed here are kept in that visitor's own browser only — they never sync
anywhere. The Claude artifact version is the one whose planner writes back to the
vault.

## Privacy

Anyone holding the URL can read the whole registry, including which events are
marked tracking or registered. The `noindex` header and meta tag keep it out of
search engines, but that is **not** access control. Keep the GitHub repository
private, and use Vercel's Deployment Protection if the URL itself needs a password.
