# CLAUDE.md — plymouth-theme-kiro-logo

## Project overview

See [README.md](./README.md) for the user-facing description of this repo.

## Current state

EDU scaffold (`setup.sh`, `up.sh`) plus the standard markdown files. The theme ships under `usr/share/plymouth/themes/kiro-logo/` and implements a "self-assembling K" animation from two colour-split logo layers.

## Patterns & decisions

- **Two-layer logo** — `logo.png` is split into `logo-body.png` (blue) and `logo-wedge.png` (green) by colour (`g>b`), with feathered complementary alpha so they recompose seamlessly. The body's notch is cut from a *dilated* wedge mask so no green edge ghosts through before the wedge slides in. See [CHANGELOG.md](./CHANGELOG.md) 2026.05.29 for the exact ImageMagick recipe.
- **Animation** — body fade-in → wedge cubic ease-out slide from lower-right → gentle sin-wave breath, all in `kiro-logo.script` at Plymouth's ~50 Hz refresh.
- **Naming** — deliberately distinct from `plymouth-theme-kiro` (which already ships to `nemesis_repo`) to avoid a package/theme-dir collision.
- Bash scripts follow the canonical Kiro-HQ template — see [Kiro-HQ/CLAUDE.md](/home/erik/Insync/Kiro/Kiro-HQ/CLAUDE.md).

## Packaging

Not yet packaged. If it should ship via `nemesis_repo`, add a PKGBUILD under `~/EDU-PKG-BUILD/edu-pkgbuild/plymouth-theme-kiro-logo/` modelled on the `plymouth-theme-kiro` one (install the `.plymouth`, `.script`, and `*.png` into `/usr/share/plymouth/themes/kiro-logo`).

## Next steps

Tracked centrally in [HQ/MASTER_TODO.md](/home/erik/Insync/Kiro/Kiro-HQ/MASTER_TODO.md).
