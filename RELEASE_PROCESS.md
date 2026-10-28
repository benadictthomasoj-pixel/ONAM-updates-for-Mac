# ONAM Release Process

This document describes the repeatable workflow for publishing a new ONAM macOS release.

---

## Prerequisites

- macOS development machine with Node.js, npm, and Electron installed.
- Access to the ONAM project repository at `git@github.com:benadictthomasoj-pixel/og-billing.git` (branch `v1-stabilization`).
- GitHub CLI (`gh`) installed and authenticated: `gh auth login`.
- Write access to `https://github.com/benadictthomasoj-pixel/ONAM-updates-for-Mac`.

---

## Step 1 — Update the Version Number

In the ONAM project:

```bash
cd "/Users/benadict/Documents/antigravity projects/billing logic"
# Edit package.json: bump "version" to the new version, e.g. "1.1.0"
npm version 1.1.0 --no-git-tag-version
```

Also update `electron/main.js` if it hard-codes a version string in the About dialog.

---

## Step 2 — Run All Tests

```bash
npm test
```

All 29 test suites must pass before building. Fix any failures before proceeding.

---

## Step 3 — Build the macOS ARM64 DMG

```bash
npm run dist:mac
# or: npx electron-builder --mac --arm64
```

This produces:
```
dist/ONAM-<version>-arm64.dmg
dist/ONAM-<version>-arm64.dmg.blockmap
dist/latest-mac.yml
dist/mac-arm64/ONAM.app
```

---

## Step 4 — Verify the Build

```bash
# Check architecture
file dist/mac-arm64/ONAM.app/Contents/MacOS/ONAM
# Expected: Mach-O 64-bit executable arm64

# Check bundle ID and display name
defaults read dist/mac-arm64/ONAM.app/Contents/Info.plist CFBundleIdentifier
defaults read dist/mac-arm64/ONAM.app/Contents/Info.plist CFBundleDisplayName

# Compute SHA-256 (record this — you'll need it)
shasum -a 256 "dist/ONAM-<version>-arm64.dmg"
```

---

## Step 5 — Mount and Spot-Check the DMG

```bash
hdiutil attach "dist/ONAM-<version>-arm64.dmg" -readonly
# Confirm: ONAM.app and Applications symlink are present
ls /Volumes/ONAM*/
hdiutil detach "/Volumes/ONAM*"
```

---

## Step 6 — Record the Checksum

Append to `CHECKSUMS.txt` in the ONAM-updates-for-Mac repository clone:

```
ONAM-<version>-arm64.dmg  <sha256>
```

---

## Step 7 — Update Documentation

In your local clone of `ONAM-updates-for-Mac`:

1. Update `README.md` — bump "Latest Release" table and "Release History" table.
2. Update `CHECKSUMS.txt` with the new checksum line.
3. Commit and push:

```bash
cd /tmp/onam-updates-repo    # or wherever you cloned it
git add README.md CHECKSUMS.txt
git commit -m "docs: ONAM v<version> release documentation"
git push origin main
```

---

## Step 8 — Create the GitHub Release

```bash
# Draft first — review before publishing
gh release create v<version> \
  "/path/to/dist/ONAM-<version>-arm64.dmg" \
  --repo benadictthomasoj-pixel/ONAM-updates-for-Mac \
  --title "ONAM <version> — macOS Apple Silicon" \
  --notes-file release-notes.md \
  --draft \
  --target main

# Review the draft at:
# https://github.com/benadictthomasoj-pixel/ONAM-updates-for-Mac/releases

# Publish when ready:
gh release edit v<version> \
  --repo benadictthomasoj-pixel/ONAM-updates-for-Mac \
  --draft=false
```

> **Important:** The tag must be `v<version>` (e.g. `v1.1.0`). The ONAM updater parses the tag to extract the semver version number.

---

## Step 9 — Verify In-App Update Detection

Launch ONAM (v<previous>) and go to **Settings → Software Updates**:

1. Select **Mac-Specific Updates (macOS only)**.
2. Click **Check for Updates**.
3. Confirm it detects v<version> as available.
4. The update system reads from: `https://api.github.com/repos/benadictthomasoj-pixel/ONAM-updates-for-Mac/releases`

---

## Step 10 — Tag the Source Repository

```bash
cd "/Users/benadict/Documents/antigravity projects/billing logic"
git tag -a v<version> -m "Release v<version>"
git push origin v<version>
```

---

## Release Checklist

- [ ] Version bumped in `package.json`
- [ ] All tests pass (`npm test`)
- [ ] DMG built (`npm run dist:mac`)
- [ ] Architecture verified as `arm64`
- [ ] DMG mounted and spot-checked
- [ ] SHA-256 recorded
- [ ] `CHECKSUMS.txt` updated and pushed
- [ ] `README.md` updated and pushed
- [ ] GitHub Release created with DMG asset
- [ ] Release published (not draft)
- [ ] In-app update detection verified
- [ ] Source repo tagged

---

## Notes on Code Signing and Notarization

ONAM is currently **ad-hoc signed** only. Users must right-click → Open on first launch.

To enable full notarization in a future release:

1. Enrol in the Apple Developer Program (https://developer.apple.com).
2. Create a Distribution certificate and export it as a `.p12`.
3. Set `CSC_LINK` and `CSC_KEY_PASSWORD` environment variables.
4. Set `APPLE_ID`, `APPLE_APP_SPECIFIC_PASSWORD`, and `APPLE_TEAM_ID` for notarization.
5. Run `npm run dist:mac` — electron-builder will sign and notarize automatically.

Until notarization is set up, always include Gatekeeper bypass instructions in release notes.

---

## Asset Naming Convention

| Platform | Pattern |
|---|---|
| macOS ARM64 | `ONAM-<version>-arm64.dmg` |
| macOS Intel | `ONAM-<version>-x64.dmg` (future) |
| Windows | `ONAM-Setup-<version>.exe` (future) |

The ONAM updater matches assets by file extension (`.dmg`) and architecture substring (`arm64`) for Apple Silicon Macs.
