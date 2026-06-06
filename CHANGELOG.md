# Changelog

## 2026.06.04

### What Changed
- **Wedge-timing fix + bigger prompt font, ported from `-nemesis` after a passing test.** The green wedge now assembles by ~0.36 s (was ~1.9 s, so it never showed inside the splash window), and the encrypted-boot "Enter Password" prompt + dots render at `Sans Bold 20` instead of Plymouth's tiny ~12 pt default.
- Both changes were validated on fresh **encrypted and unencrypted** installs via `plymouth-theme-kiro-logo-nemesis` on the `-next` ISO before landing here. The fancy login-card experiment was rejected (mispositioned on VirtualBox) — production stays KISS: simple centred prompt, no card.

### Technical Details
- `kiro-logo.script`: timeline constants `FADE_END 45→8`, `SLIDE_BEG 35→3`, `SLIDE_END 95→18` (post-assembly sin-wave breath unaffected — keys off `t − SLIDE_END`).
- Font set via the 6th `Image.Text` arg: `Image.Text("Enter Password", 1, 1, 1, 1, "Sans Bold 20")` (line ~101) and the `*` bullet likewise (line 23).
- This prompt only renders on encrypted boot once the installer uses systemd initramfs hooks (`sd-encrypt` → Plymouth's password agent); that switch shipped in parallel in `kiro-calamares-config`.

## 2026.06.03

### What Changed
- **Reverted the password-card / "boxes" experiment** — the previous `-02`-era
  commit (added `box.png`, `bullet.png`, `lock.png` and rewrote
  `kiro-logo.script` with a framed LUKS login card) broke the splash at boot:
  the logo dropped out and the prompt fell back to bare text. Rolled the source
  repo back to the clean self-assembling-K logo.

### Technical Details
- `git revert` of the boxes commit (new revert commit, history intact — no
  force-push). Removed `box.png` / `bullet.png` / `lock.png`; restored the prior
  `kiro-logo.script`. Theme dir back to `kiro-logo.plymouth`, `kiro-logo.script`,
  `logo.png`, `logo-body.png`, `logo-wedge.png`.
- The card experiment is preserved in the parallel
  `plymouth-theme-kiro-logo-nemesis` repo (opt-in `conflicts`-only package),
  where the wedge-timing and asset-packaging fixes were worked out and
  render-tested.
- The served `nemesis_repo` package was independently rolled back `26.06-02 → -01`.

### Files Modified
- `usr/share/plymouth/themes/kiro-logo/kiro-logo.script` (restored)
- `usr/share/plymouth/themes/kiro-logo/box.png`, `bullet.png`, `lock.png` (removed)

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
