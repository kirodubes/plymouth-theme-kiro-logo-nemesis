<p align="center">
  <img src="kiro.jpg" alt="Kiro" width="220" />
</p>

# plymouth-theme-kiro-logo

A Plymouth boot splash theme for Arch / Kiro Linux built around the Kiro **K** logo — a "self-assembling" animation where the blue body fades in and the green wedge eases into place. Part of the `~/EDU/` learning series and a sibling to [plymouth-theme-kiro](https://github.com/erikdubois/plymouth-theme-kiro) and [plymouth-theme-startrek](https://github.com/erikdubois/plymouth-theme-startrek).

## What's in this repo

- `usr/share/plymouth/themes/kiro-logo/` — the Plymouth theme assets:
  - `kiro-logo.plymouth` — theme manifest
  - `kiro-logo.script` — the self-assembling animation
  - `logo.png` — the full Kiro logo (source asset)
  - `logo-body.png` / `logo-wedge.png` — the two layers the logo is split into (blue body + green wedge), cut from the same canvas so they align pixel-perfect
- `setup.sh`, `up.sh` — standard EDU bash scaffold.

## How the animation works

The logo is split by colour into two layers (green wedge where green dominates blue, blue body elsewhere) with feathered, complementary alpha so the layers recompose into the original logo with no seam. The body's notch is cut a few pixels wider than the wedge so no green edge ghosts through before the wedge arrives. On boot the blue body fades in, the green wedge eases in from the lower-right and locks into the notch, then the whole mark breathes gently.

## Installation

### Manual

```bash
git clone https://github.com/erikdubois/plymouth-theme-kiro-logo.git
cd plymouth-theme-kiro-logo
sudo cp -r usr/share/plymouth/themes/. /usr/share/plymouth/themes/
```

You'll also need Plymouth:

```bash
sudo pacman -S plymouth
```

### Activate

Add `plymouth` to `HOOKS` in `/etc/mkinitcpio.conf` (immediately after `base udev`), then set the theme and rebuild initramfs:

```bash
sudo plymouth-set-default-theme -R kiro-logo
```

Make sure `quiet splash` is in `GRUB_CMDLINE_LINUX_DEFAULT` in `/etc/default/grub`, then regenerate the GRUB config:

```bash
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

Reboot.

## Test without rebooting

```bash
sudo plymouthd --debug --tty=tty1
sudo plymouth --show-splash
# leave it visible for a few seconds, then:
sudo plymouth --quit
```

Run from a TTY (Ctrl+Alt+F2), not from inside your graphical session.

## Websites

Information : https://erikdubois.be

## Social Media

Youtube : https://www.youtube.com/erikdubois

## License

See [LICENSE](./LICENSE).
