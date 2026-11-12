# Maintainer: Fill in AUR username and contact before publishing.
pkgname=mydesk
# Independent package version: increment when any pinned upstream commit changes.
pkgver=1.0.3
# Increment only when packaging rules change without changing pinned sources.
pkgrel=1
pkgdesc='Customized Niri desktop stack with Xwayland and persistent tray watcher'
arch=('x86_64')
url='https://github.com/sidgwick/niri'
license=('GPL-3.0-or-later' 'MPL-2.0' 'MIT')
depends=(
  bash
  bluez
  brightnessctl
  cairo
  cliphist
  dbus
  dog-samurai
  fcitx5
  fcitx5-chinese-addons
  fcitx5-gtk
  fcitx5-qt
  fuzzel
  gcc-libs
  glib2
  glibc
  grim
  iwd
  jq
  kvantum
  libdisplay-info
  libinput
  libnotify
  libpipewire
  libxcb
  libxkbcommon
  mesa
  network-manager-applet
  networkmanager
  noto-fonts
  noto-fonts-cjk
  noto-fonts-emoji
  otf-font-awesome
  pango
  pipewire
  pipewire-alsa
  pipewire-audio
  pipewire-pulse
  pixman
  playerctl
  polkit-gnome
  qt6ct
  seatd
  sddm
  slurp
  swaybg
  swayidle
  swaylock-effects-git
  swaync
  swappy
  systemd-libs
  waybar
  wayland
  wireplumber
  wl-clipboard
  xcb-util-cursor
  xdg-desktop-portal
  xdg-desktop-portal-gnome
  xdg-desktop-portal-gtk
  xdg-utils
  xorg-xwayland
)
makedepends=(
  clang
  git
  pkgconf
  rust
)
optdepends=(
  'bibata-cursor-theme: cursor theme selected by the desktop configuration'
  'cc-switch-bin: optional application started by the personal Niri configuration'
  'clash-verge-rev-bin: optional application started by the personal Niri configuration'
  'fonts-apple: PingFang and Monaco font fallback'
  'kitty: terminal emulator used by Niri keybindings and Fuzzel'
  'libqalculate: provides qalc for the Fuzzel calculator script'
  'otf-apple-sf-pro: SF Pro font family used by the desktop configuration'
  'pavucontrol: audio control panel opened from Waybar'
  'thunar: file manager used by Niri keybindings'
  'wlogout: power menu opened from Waybar'
  'whitesur-icon-theme-git: icon theme used by Fuzzel'
  'wtype: sends Shift+Insert when restoring clipboard history'
)
provides=(
  'niri=26.4.0'
  'sni-watcher=0.3.0'
  'wayland-compositor'
  'xwayland-satellite=0.8.3'
)
conflicts=('niri' 'sni-watcher' 'xwayland-satellite')
replaces=('niri' 'sni-watcher' 'xwayland-satellite')

_niri_commit=02a4aea9485b9b7329b571c6270bcb0121cf0a9e
_satellite_commit=116451567841f8ff410863e22f3904c1ba0b69b1
_sni_watcher_commit=927995fc1596b8f23eae02c6ecd58652dd06433a
source=(
  "niri::git+https://github.com/sidgwick/niri.git#commit=$_niri_commit"
  "xwayland-satellite::git+https://github.com/sidgwick/xwayland-satellite.git#commit=$_satellite_commit"
  "sni-watcher::git+https://github.com/MasonRhodesDev/sni-watcher.git#commit=$_sni_watcher_commit"
)
sha256sums=(
  'SKIP'
  'SKIP'
  'SKIP'
)

prepare() {
  local target
  target="$(rustc -vV | sed -n 's/^host: //p')"

  for component in niri xwayland-satellite sni-watcher; do
    cd "$srcdir/$component"
    cargo fetch --locked --target "$target"
  done

  cd "$srcdir/xwayland-satellite"
  sed -i 's|/usr/local/bin/xwayland-satellite|/usr/bin/xwayland-satellite|' \
    resources/xwayland-satellite.service
}

build() {
  cd "$srcdir/niri"
  export NIRI_BUILD_COMMIT="$(git rev-parse --short HEAD)"
  CFLAGS+=' -ffat-lto-objects'
  cargo build --frozen --release

  for shell in bash fish zsh; do
    cargo run --frozen --release --bin niri -- \
      completions "$shell" > "$shell-completions"
  done

  cd "$srcdir/xwayland-satellite"
  cargo build --frozen --release --features systemd

  cd "$srcdir/sni-watcher"
  cargo build --frozen --release
}

package() {
  cd "$srcdir/niri"
  install -Dm755 target/release/niri "$pkgdir/usr/bin/niri"
  install -Dm755 resources/niri-session "$pkgdir/usr/bin/niri-session"
  install -Dm644 resources/niri.service \
    "$pkgdir/usr/lib/systemd/user/niri.service"
  install -Dm644 resources/niri-shutdown.target \
    "$pkgdir/usr/lib/systemd/user/niri-shutdown.target"
  install -Dm644 resources/niri.desktop \
    "$pkgdir/usr/share/wayland-sessions/niri.desktop"
  install -Dm644 resources/default-config.kdl README.md \
    -t "$pkgdir/usr/share/doc/niri/"
  install -Dm644 bash-completions \
    "$pkgdir/usr/share/bash-completion/completions/niri"
  install -Dm644 fish-completions \
    "$pkgdir/usr/share/fish/vendor_completions.d/niri.fish"
  install -Dm644 zsh-completions \
    "$pkgdir/usr/share/zsh/site-functions/_niri"
  install -Dm644 LICENSE "$pkgdir/usr/share/licenses/mydesk/niri/LICENSE"

  cd "$srcdir/xwayland-satellite"
  install -Dm755 target/release/xwayland-satellite \
    "$pkgdir/usr/bin/xwayland-satellite"
  install -Dm644 resources/xwayland-satellite.service \
    "$pkgdir/usr/lib/systemd/user/xwayland-satellite.service"
  install -Dm644 README.md \
    "$pkgdir/usr/share/doc/xwayland-satellite/README.md"
  install -Dm644 LICENSE \
    "$pkgdir/usr/share/licenses/mydesk/xwayland-satellite/LICENSE"

  cd "$srcdir/sni-watcher"
  install -Dm755 target/release/sni-watcher \
    "$pkgdir/usr/bin/sni-watcher"
  install -Dm644 README.md \
    "$pkgdir/usr/share/doc/sni-watcher/README.md"
  install -Dm644 LICENSE \
    "$pkgdir/usr/share/licenses/mydesk/sni-watcher/LICENSE"
}
