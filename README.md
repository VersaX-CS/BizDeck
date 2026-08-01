# BizDeck — Unified Deliverable

This folder replaces the two overlapping packages from the original zip
(the top-level `index.html`/`manifest.json` set, and the nested
`BizDeck-Enhanced.zip`) with **one** clean, deployable PWA.

## What was merged

- **App shell:** built on `BizDeck-Enhanced/BizDeck/index.html`, since a
  diff showed it was the newer of the two app copies — it includes
  everything the older "BizHub"-branded copy had, plus the cloud-backup
  feature and a fuller PWA `<head>` (favicons, iOS/Android meta tags).
- **`manifest.json`:** rebuilt by combining the more complete metadata
  from the top-level manifest (`id`, `display_override`, `lang`/`dir`,
  screenshots) with the shortcuts and branding that actually match this
  app's tabs (Dashboard / Clients / Records / Catalogue).
- **`sw.js`:** carried over unchanged — its cached file list already
  matches the icon filenames used here.

## Icon fixes

**1. The SVG icons didn't match the PNG artwork.** The old hand-coded
`icon-512x512.svg` etc. drew a rough, broken-looking "B" — bars sitting
in a separate box, a disconnected arrow — nothing like the clean logo in
`icon-512.png`. Rather than continue hand-tracing gradients and getting
it slightly wrong again, I generated the new SVGs by embedding the
correct PNG artwork directly inside a normal `<svg><image>` wrapper.
That's a completely standard technique for PWA icons and guarantees the
SVG renders **pixel-identical** to the PNG at any size — verified this
numerically (rendered each SVG back to a bitmap with `rsvg-convert` and
diffed it against the source PNG).

**2. Made the main icon transparent.** `icon-512.png` and `icon-192.png`
had a plain white square canvas around the badge. I removed just that
outer canvas — using a flood fill from the corners so only the
background is cleared, not the white "B" letterform or white accents
inside the logo — with a soft anti-aliased edge so it doesn't look
cut out. `icon-512.svg` / `icon-192.svg` embed this transparent version.

**3. Maskable and Apple touch icons were left opaque, on purpose.**
I checked whether transparency would actually help here, and it wouldn't:
- **Maskable icons** are meant to fill the entire canvas — platforms
  (Android adaptive icons in particular) apply their own mask shape, and
  a transparent maskable icon can show inconsistent results across
  launchers. The spec's own guidance is to avoid transparency here.
- **Apple touch icons** are explicitly flattened by iOS — any
  transparent pixels get rendered as **black** on the home screen, not
  see-through. A transparent version would look broken, not better.

So `icon-512-maskable.*` and `apple-touch-icon.*` keep their original
opaque backgrounds (regenerated as SVGs the same exact-match way, so
they're still pixel-correct), while the everyday app icon
(`icon-512`/`icon-192`) is transparent as requested.

## App fixes (this update)

- **Real Estate had no icon.** Its `CATEGORY_CONFIG` entry pointed at an
  icon key called `"home"`, but no `"home"` icon was ever defined in the
  `ICONS` object — so it silently rendered nothing. Added the missing
  house icon.
- **"Admin Control Panel" was listed as a business type.** The signup
  category picker looped over the full internal category list, which
  includes the hidden admin mode. Admin access is meant to be unlocked
  only via the admin key/credentials, not tapped as a business type, so
  it's now filtered out of that picker specifically (it's left in place
  everywhere else it's used internally, like the universal-access module
  switcher).
- **Cloud folder backup connected but couldn't write.** The explicit
  "Backup now" button awaited `idbGet()` and then `queryPermission()`
  *before* ever calling `requestPermission()`. Each `await` in between
  risks losing the click's transient user-activation, so by the time
  `requestPermission()` ran, the browser had nothing to anchor the
  prompt to and silently treated it as refused — folders connected fine
  (that happens inside the picker itself, a real gesture) but every
  backup after that failed. Explicit backups now call
  `requestPermission()` directly after the one unavoidable `idbGet()`;
  silent/background backups still only ever peek with `queryPermission()`
  and never prompt.
- **Added password & key files.** The Account page's credentials card
  could only show/copy the access key. It now also shows the account
  password (masked, with show/copy) and has **Download key file** and
  **Download password file** buttons, so both can be saved as plain-text
  files, next to a note to store them somewhere private and delete them
  from Downloads afterwards.
- **Policy text turned into a button.** The "Data & privacy note" card
  on the Account page used to dump the entire policy inline. It's now a
  short blurb with a **View data & privacy policy** button that opens
  the same text in a modal.

## Files

```
index.html               the app
manifest.json             merged PWA manifest
sw.js                      service worker
icon-512.png / .svg        transparent, exact match
icon-192.png / .svg        transparent, exact match
icon-512-maskable.png / .svg   opaque (by design, see above)
apple-touch-icon.png / .svg    opaque (by design, see above)
favicon-16.png, favicon-32.png
```

Deploy by uploading this whole folder to your web root — no build step
needed.
