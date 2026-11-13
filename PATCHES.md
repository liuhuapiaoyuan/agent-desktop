# Windows patch set for agent-desktop v1.3.0

This repository is a **modified fork** of [vercel-labs/agent-desktop](https://github.com/vercel-labs/agent-desktop),
Apache-2.0. The `xingbao/windows-fixes` branch adds three patches on top of upstream v1.3.0:

| Commit | Change |
|---|---|
| `51dba69` | Cherry-pick of upstream PR #651: `fix(windows): recover foreground activation through UI Automation` |
| `aab1c4d` | `fix(windows): tolerate UWP HWND ownership change during show verbs` |
| `ad4e3eb` | `fix(windows): synthesize wheel for headed scroll without ScrollPattern` |

## Build (Windows)

Requirements: Rust 1.89+, MSVC Build Tools with the Windows SDK (or the GNU toolchain as a fallback).

```powershell
git clone https://github.com/liuhuapiaoyuan/agent-desktop.git
cd agent-desktop
git checkout xingbao/windows-fixes
cargo build --release --locked -p agent-desktop
# target/release/agent-desktop.exe
```

CI builds the same source on `windows-latest` for tags matching `xingbao-v*`
(see `.github/workflows/xingbao-release.yml`).

## Verification

- `focus-window` on Electron/UWP: `ok:true`, `is_focused:true`;
- UWP `minimize`/`restore`: `delivered_verified`;
- headed click after foreground fix: `ok:true`;
- `scroll --headed` on Notepad text/Chromium document: `ok:true` (`SendInput.wheel`);
- protocol 2.6 unchanged; `desktop:check` and `test:desktop:win` (12 PASS) pass.

Headless scroll remains fail-closed when the target has no ScrollPattern; text controls still require
`set-value`/typing instead of `click`.

## License / notice

Upstream code and binary are Apache-2.0; see `LICENSE`. This fork's modifications are listed above.
The Windows binary in the release is a modified build, not the upstream artifact.
