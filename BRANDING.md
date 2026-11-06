# Skydimo — branding patch notes

This fork is a personal, unofficial rebrand of
[RustDesk](https://github.com/rustdesk/rustdesk) for the maintainer's own use
and their friends. **It is not RustDesk / Purslane Tech Pte. Ltd., and is not
affiliated with or endorsed by them.** RustDesk is licensed under
AGPL-3.0 (see `LICENCE` — unchanged); this fork keeps that license and the
original copyright notices wherever they appear, and only relabels
user-visible branding.

## What changed from upstream RustDesk

| Area | File(s) | Change |
|---|---|---|
| Runtime app name (window titles, tray tooltip, config dir, URI scheme) | `src/core_main.rs` | Sets `hbb_common::config::APP_NAME` to `"Skydimo"` at startup, before anything reads it. Done here rather than in `libs/hbb_common/src/config.rs` (a separate git submodule shared with the RustDesk server) to avoid needing a parallel fork of that submodule just to change one string |
| Windows DLL/exe version metadata | `Cargo.toml` (`[package.metadata.winres]`) | ProductName/FileDescription/CompanyName/LegalCopyright/OriginalFilename updated; copyright line keeps the RustDesk/Purslane attribution and AGPL-3.0 mention |
| Flutter Windows runner exe version metadata | `flutter/windows/runner/Runner.rc` | Same fields as above, for the actual Flutter-built `.exe` |
| Flutter Windows runner window-finder fallback name | `flutter/windows/runner/main.cpp` | Fallback literal `L"RustDesk"` → `L"Skydimo"` (only used if the FFI app-name lookup fails) |
| App icons | `res/icon.ico`, `res/icon.png`, `res/tray-icon.ico`, `flutter/windows/runner/resources/app_icon.ico` | Replaced with the Skydimo logo (source vector in `branding/logo/`), rendered at all sizes Windows needs (16–256px icons, 1024px master PNG) |
| About screen | `flutter/lib/desktop/pages/desktop_setting_page.dart` (`_About` widget) | Title changed to "About Skydimo"; added an explicit "this is an unofficial, independent build, not affiliated with RustDesk/Purslane" notice; copyright block keeps the RustDesk/Purslane AGPL-3.0 attribution; "Website" link now points at this fork's repo instead of rustdesk.com; "Privacy Statement" link still points at RustDesk's real privacy page, since the public RustDesk relay/rendezvous servers (operated by RustDesk/Purslane, not by this fork) are what's actually processing that data |
| Windows MSI installer branding | `.github/workflows/windows-skydimo-build.yml` (new) | `res/msi/preprocess.py` is invoked with `--app-name Skydimo --manufacturer Skydimo` (RustDesk's own supported white-label mechanism — see `res/msi/README.md`), with the exe copied to `Skydimo.exe` for the dist dir it scans. The installer's license/EULA screen (`res/msi/Package/License.rtf`) is **not** run through upstream's auto-substitution (that file is actually RustDesk's own privacy-policy text for their company/servers, not a generic EULA — blindly renaming "RustDesk"/"Purslane" to "Skydimo" there would make false privacy claims); instead the CI step overwrites it with a short, accurate notice (unofficial build, AGPL-3.0, link to this fork and to RustDesk's real privacy policy) |
| CI build scope | `.github/workflows/flutter-build.yml` | Added an opt-in `windows_only` input (default `false`, so the official nightly/tag workflows are unaffected) that skips every job except the Windows Flutter build and its direct dependencies, and the Windows build matrix is trimmed to `x86_64-pc-windows-msvc` only |

## What was *not* changed

- `LICENCE` (full AGPL-3.0 text) — untouched.
- The Rust/Dart source logic, protocol, server code — untouched, this is a
  cosmetic rebrand only.
- The actual on-disk binary produced by `flutter build windows` is still
  named `rustdesk.exe` internally (its `BINARY_NAME` in
  `flutter/windows/runner/CMakeLists.txt` was deliberately left alone, to
  avoid touching the large, carefully-tuned Windows CI build sequence that
  references that filename in ~15 places). Only the **installed, user-facing**
  copy — the one packaged into the MSI — is renamed to `Skydimo.exe`.
- Server-side code / default servers: this build still points at RustDesk's
  public rendezvous/relay servers (`rs-ny.rustdesk.com`) unless reconfigured
  by the user in Settings.

## Rebuilding

The changes above are plain commits on top of an upstream RustDesk clone, so
`git log` / `git diff <upstream-commit>..HEAD` reproduces this exact patch set
and it can be rebased onto a newer RustDesk release the same way.
