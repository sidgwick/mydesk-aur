# mydesk

`mydesk` is an x86_64 Arch Linux package for a desktop environment built around
customized Niri. It builds Niri, xwayland-satellite, and sni-watcher from the
commits pinned in `PKGBUILD`, and installs the required desktop software.

The package provides and replaces `niri`, `xwayland-satellite`, and
`sni-watcher`. Do not install those packages alongside `mydesk`.

## Install

After the package is published to the AUR, an AUR helper can install it and its
required AUR dependencies:

```sh
yay -S mydesk
```

`swaylock-effects-git` and [`dog-samurai`](https://aur.archlinux.org/packages/dog-samurai)
are required AUR dependencies. `dog-samurai` provides the SDDM theme selected
by the personal system configuration. When building manually with `makepkg`,
install both packages first; `makepkg` does not fetch or build dependencies
from the AUR.

## Configure the desktop

`mydesk` installs software and upstream session resources. It does not install
dotfiles, write files under `/etc`, enable services, or create files in a user's
home directory. Apply the matching dotfiles separately.

For a fresh system using NetworkManager, enable the system services manually:

```sh
sudo systemctl enable --now NetworkManager.service bluetooth.service
```

The dotfiles configure NetworkManager to use iwd; do not enable `iwd.service`
separately. If SDDM is the selected display manager, enable it separately:

```sh
sudo systemctl enable --now sddm.service
```

The dotfiles provide the `sni-watcher` user service and the Waybar startup
script starts it. The package installs the upstream Niri and
xwayland-satellite user units but does not enable them. Sign in to the Niri
session from the display manager after applying the desktop configuration.

Commonly used applications such as Kitty, Thunar, pavucontrol, and the
FuzzelCalc `qalc` command are optional. Other AUR applications and themes are
listed as optional dependencies; `pacman` will show those hints but will not
install them automatically.

## KVM validation

The build and desktop checks were run in an Arch Linux KVM guest. See
[`VM-TEST-REPORT.md`](VM-TEST-REPORT.md) for results and known hardware limits,
and [`VM-README.md`](VM-README.md) for commands to open the test VM and preserve
the clean clone.
