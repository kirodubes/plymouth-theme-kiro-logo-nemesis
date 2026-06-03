# Changelog

## 2026.06.03

### What Changed
- **Login-card LUKS prompt.** The encrypted-boot password prompt is now a framed card centred below the logo — dark fill with a Kiro-blue border (`box.png`), a lock icon at the left (`lock.png`), the typed characters as blue dots (`bullet.png`), and an "enter passphrase" label above — replacing the bare white "Enter Password" text + asterisks.

### Technical Details
- New PIL-generated assets: `box.png` (440×64 rounded card, fill `#0C1B33`, 2px `#0195F7` border), `lock.png` (34×34 padlock), `bullet.png` (Kiro-blue dot with built-in spacing).
- `kiro-logo.script`: `DisplayPasswordCallback` rewritten to the box+lock+dots pattern from the stock `script` theme — `card_setup()` builds the sprites once at high Z, dots laid out after the lock, `card_opacity()` + `DisplayNormalCallback` hide it on unlock. Card centred at `screen.h * 0.82` to clear the 40%-height logo.
- This prompt only renders now because the installer switched to systemd initramfs hooks (`sd-encrypt` → Plymouth's built-in password agent); with the old busybox `encrypt` hook the script callback never painted.
- **Not yet render-tested on real hardware/VM** — verified via a static PIL mockup using the same assets + layout math; needs a `plymouthd --debug` / encrypted-boot pass to confirm positioning and the `Image.Text` label font (may swap to a baked `label.png` if it looks plain).

## 2026.05.29

### What Changed
- New repo `plymouth-theme-kiro-logo` scaffolded in `~/EDU/` from the existing EDU Plymouth theme repos (`plymouth-theme-kiro`, `plymouth-theme-startrek`). A distinct name was chosen to avoid colliding with the existing `plymouth-theme-kiro` package, which already ships into `nemesis_repo`.
- Built the "self-assembling K" animation: the blue logo body fades in, the green wedge eases in from the lower-right and locks into place, then the whole mark breathes gently.
- Split the source `logo.png` (1024x948 RGBA) into two layers — `logo-body.png` (blue K) and `logo-wedge.png` (green wedge) — and smoothed the cut on both.

### Technical Details
- **Colour split** — the green wedge is isolated with `-fx '(u.a>0.5 && u.g>u.b) ? 1 : 0'` (opaque pixels where green dominates blue); the body is everything else. Both layers are kept on the full 1024x948 canvas so identical sprite coordinates align them pixel-perfect in the script.
- **Feathered, complementary alpha** — the binary boundary mask is blurred (`-blur 0x2`) and each layer's alpha is `orig_alpha × softmask` / `orig_alpha × (1−softmask)`. The two alphas sum back to the original, so the layers recompose with no seam while both keep the original soft outer anti-aliasing.
- **No ghost edge** — the body's notch is cut from a *dilated* wedge mask (`-morphology Dilate Disk:4 -blur 0x2`) so the antialiased green boundary pixels don't ghost through the body before the wedge arrives. The wedge keeps its true (non-dilated) edge and overlaps the few-pixel gap when it lands.
- **Script** — `kiro-logo.script` scales both layers to 40% of screen height, fades the body in over ~45 ticks, eases the wedge in (cubic ease-out, `1−(1−p)³`) from a lower-right offset between ticks 35–95, then runs a subtle sin-wave opacity breath (0.9→1.0) on the assembled mark. Standard Plymouth password/question/message/normal callbacks included.
- Reused the verbatim EDU `setup.sh` / `up.sh` (both derive the project name from the folder, so they work unchanged) and `LICENSE`.

### Files Modified
- `usr/share/plymouth/themes/kiro-logo/kiro-logo.plymouth` (created)
- `usr/share/plymouth/themes/kiro-logo/kiro-logo.script` (created)
- `usr/share/plymouth/themes/kiro-logo/logo-body.png` (created — split layer)
- `usr/share/plymouth/themes/kiro-logo/logo-wedge.png` (created — split layer)
- `setup.sh`, `up.sh`, `LICENSE`, `.gitignore`, `kiro.jpg` (copied scaffold)
- README.md, CHANGELOG.md, CLAUDE.md, IDEAS.md (created)
