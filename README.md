# Magyar Út — Hungarian Course

A self-contained A1–A2 Hungarian course: lessons, exercises and flashcards, built as a
static site that installs as an app on Android and iPhone.

**Live:** https://r4d0slav.github.io/hungarian_course/

## Install on your phone

| Platform | Steps |
| --- | --- |
| **Android** (Chrome) | Open the link → Chrome offers **Install app**, or ⋮ menu → *Add to Home screen* |
| **iPhone** (Safari — not Chrome) | Open the link → Share button (□↑) → *Add to Home Screen* → **Add** |

It opens fullscreen with no browser bars and works offline after the first visit.
Lesson progress is saved on-device with `localStorage`.

## Structure

```
.
├── index.html              Course home — level picker and overview
├── hungarian_a1.html       A1 course (lessons, exercises, flashcards)
├── hungarian_a2.html       A2 course
├── manifest.json           PWA metadata: name, icons, theme, shortcuts
├── sw.js                   Service worker — offline caching
├── favicon.ico             Browser tab icon (kept at root by convention)
└── icons/
    ├── icon.svg                  Master artwork — everything below is rendered from it
    ├── icon-maskable.svg         Same, scaled to 78% for Android's mask safe-zone
    ├── icon-192.png              Android home screen
    ├── icon-512.png              Splash screen / store listing
    ├── icon-maskable-512.png     Android adaptive icon
    └── apple-touch-icon.png      iOS home screen (180×180)
```

`manifest.json` and `sw.js` must stay at the repo root: a service worker can only control
pages at or below its own directory, so moving `sw.js` into a subfolder would silently
shrink its scope to that subfolder.

## Deploying

GitHub Pages serves the `main` branch root, so publishing is just:

```bash
git push origin main
```

The change is live in under a minute. The service worker is **network-first for HTML**, so
installed phones pick up edited lessons on the next open — no cache-busting needed. Static
assets (icons, Google Fonts) are cache-first with a background refresh, which is what makes
the app work offline.

When you change which files are precached, bump `VERSION` in `sw.js` to evict the old cache.

## Regenerating icons

Edit `icons/icon.svg`, then re-render the raster sizes:

```bash
cd icons
rsvg-convert -w 192 -h 192 icon.svg          -o icon-192.png
rsvg-convert -w 512 -h 512 icon.svg          -o icon-512.png
rsvg-convert -w 180 -h 180 icon.svg          -o apple-touch-icon.png
rsvg-convert -w 512 -h 512 icon-maskable.svg -o icon-maskable-512.png
rsvg-convert -w  32 -h  32 icon.svg          -o /tmp/favicon-32.png
convert /tmp/favicon-32.png ../favicon.ico
```

Needs `librsvg2-bin` and `imagemagick`.

## Local preview

```bash
python3 -m http.server 8000
```

Then open http://localhost:8000. Service workers need `http://localhost` or HTTPS —
opening the HTML files directly with `file://` will not register one.
