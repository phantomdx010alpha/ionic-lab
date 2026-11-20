# Ionic Lab: deployment instructions

> **For GitHub Copilot / whoever is setting this up:** this repository is a finished, static web app. It needs **no build step, no npm, no bundler and no framework**. Your only job is to publish these files on GitHub Pages **exactly as they are**.

## What this is

**Ionic Lab** is a holographic 3D human anatomy atlas (skeleton, muscles and organs) built as an installable phone app (PWA). It is made to be added to an iPhone Home Screen. In a normal browser tab it shows only an "install me" screen and does not load the atlas.

## Files (all at the repository root)

| File | Purpose |
|---|---|
| `index.html` | The whole app UI and logic, plus the install gate |
| `three.min.js` | Three.js r128, used for 3D rendering |
| `atlas.dat` | Binary mesh data (about 14 MB). **Binary. Never edit, reformat or re-encode it** |
| `meta.json` | Part names, systems and sizes, read together with `atlas.dat` |
| `sw.js` | Service worker for offline use |
| `manifest.webmanifest` | App name, icons and display settings |
| `icon-180.png`, `icon-192.png`, `icon-512.png` | Strawberry app icons. **Binary, do not modify** |

## Rules (important)

1. Keep every file at the **repo root**. Do not move them into subfolders.
2. Do **not** modify, minify, convert or "fix" `atlas.dat`, the PNG icons or `three.min.js`. Treat them as binary.
3. Keep all paths **relative** (they are). The site will be served from `https://<user>.github.io/<repo>/`, not from the domain root.
4. Do not add a bundler, framework, Jekyll theme or build pipeline.
5. Do not remove the install gate in `index.html`. It is intentional.
6. Make sure the binary files are committed without line-ending conversion. If a `.gitattributes` is wanted, use:
   ```
   *.dat binary
   *.png binary
   ```

## Steps

### 1. Commit everything to the `main` branch root

Make sure all 9 files above are in the repo root on `main`. Check afterwards that `atlas.dat` is about 14 MB and not a tiny or corrupted file.

### 2. Enable GitHub Pages

Settings → Pages → Build and deployment:
- Source: **Deploy from a branch**
- Branch: **main**, folder **/ (root)** → Save

Or with the GitHub CLI:

```bash
gh api -X POST repos/OWNER/REPO/pages -f "source[branch]=main" -f "source[path]=/"
```

(The repository must be **Public** on a free GitHub plan.)

### 3. Wait for the deployment

It takes 1 to 3 minutes. The site will be at:

```
https://OWNER.github.io/REPO/
```

### 4. Verify (desktop)

Open `https://OWNER.github.io/REPO/#dev`. The `#dev` bypasses the install gate for testing.

Expected result:
- A loading bar, then a 3D red-muscle and bone body on a dark holographic background.
- A counter reading `962 MESHES`.
- Bottom tabs: **LAYERS**, **FIND**, **VIEW**.
- No red errors in the browser console.

Then open `https://OWNER.github.io/REPO/` **without** `#dev`. It should show only the strawberry icon and the install steps. This is correct.

If you get a 404 for `atlas.dat`, `meta.json` or `three.min.js`, a file is missing or in a subfolder. Fix the location, do not change the code.

### 5. Install on iPhone (for the user)

1. Open `https://OWNER.github.io/REPO/` in **Safari** on the iPhone.
2. Tap **Share** (square with an arrow), then **Add to Home Screen**, then **Add**.
3. Open the strawberry from the Home Screen, **on Wi-Fi** the first time. It downloads about 14 MB, then works offline.

## Updating later

1. Replace or edit the files.
2. In `sw.js`, bump the version string `ionic-lab-v4` to `ionic-lab-v5` (and so on), so installed copies refresh.
3. Commit to `main`. On the phone, fully close and reopen the app once.

## Troubleshooting

| Problem | Fix |
|---|---|
| Blank page or 404 | Pages not enabled, or files not at the repo root |
| Stuck loading bar | `atlas.dat` or `meta.json` missing, or `atlas.dat` was corrupted by a text conversion. Re-upload the original binary |
| Old version after an update | Bump the version in `sw.js` and reopen the app |
| No "Add to Home Screen" on iPhone | Use Safari, not an in-app browser such as Instagram |
| Very old iPhone crashes | Lower quality in the app: VIEW → QUALITY → LOW |

## Credits

3D anatomy geometry: BodyParts3D 4.0, © Database Center for Life Science, licensed CC BY 4.0, via the open-source `ashemag/human-atlas` repository (simplified). Educational visualization, not a clinical tool.
