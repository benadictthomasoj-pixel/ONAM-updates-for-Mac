# ONAM — macOS Update Distribution

This repository is the **official release channel** for ONAM macOS updates.

ONAM is a Retail POS & Inventory Management application built for Apple Silicon Macs.

---

## Latest Release

| Field | Value |
|---|---|
| **Version** | 1.0.0 |
| **Platform** | macOS — Apple Silicon (ARM64) |
| **Installer** | `ONAM-1.0.0-arm64.dmg` |
| **SHA-256** | `b8e446977b1eb878c8fd3777f5ea32749dcd7df639337f7230b16a1dd1ab9604` |
| **Release date** | 2026-10-28 |
| **Status** | Initial release |

---

## Installation

### Step 1 — Download

Download `ONAM-1.0.0-arm64.dmg` from the [Releases](https://github.com/benadictthomasoj-pixel/ONAM-updates-for-Mac/releases) page.

### Step 2 — Verify (optional but recommended)

```bash
shasum -a 256 ~/Downloads/ONAM-1.0.0-arm64.dmg
# Expected: b8e446977b1eb878c8fd3777f5ea32749dcd7df639337f7230b16a1dd1ab9604
```

### Step 3 — Install

1. Double-click the DMG to mount it.
2. Drag **ONAM.app** into the **Applications** folder shortcut.
3. Eject the DMG.

### Step 4 — First launch (Gatekeeper bypass)

Because ONAM is **not yet notarized** with Apple, Gatekeeper will block the first launch with a message like _"ONAM cannot be opened because it is from an unidentified developer."_

To bypass this:

1. In **Finder**, navigate to `/Applications`.
2. **Right-click** (or Control-click) `ONAM.app`.
3. Choose **Open** from the context menu.
4. Click **Open** in the security dialog.

You only need to do this once. Subsequent launches work normally.

> **Why is this needed?** ONAM is currently ad-hoc signed. Full Apple Developer notarization requires an active Apple Developer Program membership and will be added in a future release.

---

## System Requirements

- macOS 12 Monterey or later
- Apple Silicon (M1, M2, M3, or M4 — ARM64 architecture)
- ~500 MB free disk space
- Internet connection (for update checks; optional for offline POS operation)

---

## In-App Updates

ONAM includes a built-in software update system. When a new release is published here:

1. Open ONAM → go to **Settings → Software Updates**.
2. Select **Mac-Specific Updates (macOS only)** as the update source.
3. Click **Check for Updates**.
4. If a new version is found, click **Download & Install**, then restart when prompted.

> **Note:** The in-app updater currently downloads and verifies the DMG by SHA-256 checksum. Automatic DMG mounting and in-place replacement will be added in a future release.

---

## Release History

| Version | Date | Notes |
|---|---|---|
| 1.0.0 | 2026-10-28 | Initial macOS release. Apple Silicon ARM64. |

---

## Repository Structure

```
ONAM-updates-for-Mac/
├── README.md           ← This file
├── RELEASE_PROCESS.md  ← How to publish a new release
└── CHECKSUMS.txt       ← SHA-256 checksums for all releases
```

Release assets (DMG installers) are attached to **GitHub Releases** — they are NOT committed to this repository's git history.

---

## Security & Code Signing

| Attribute | Status |
|---|---|
| Code signing | Ad-hoc signed (not Apple Developer ID) |
| Notarization | Not yet notarized |
| DMG signing | Unsigned |
| SHA-256 verification | ✅ Verified in-app and documented here |

Full Apple Developer signing and notarization is planned for a future release.
