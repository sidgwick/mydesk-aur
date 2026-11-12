# Maintainer: replace this line with your AUR name and contact before publishing
pkgname=mydesk
pkgver=26.04.0_0.8.3
# Bump pkgrel whenever either pinned upstream commit changes.
pkgrel=1
pkgdesc='Customized Niri compositor and Xwayland satellite'
arch=('x86_64')
url='https://github.com/sidgwick/niri'
license=('GPL-3.0-or-later' 'MPL-2.0')
depends=(
  cairo
  glib2
  glibc
  libdisplay-info
  libgcc
  libinput
  libpipewire
  libxkbcommon
  libxcb
  mesa
  pango
  pixman
  seatd
  systemd-libs
  xcb-util-cursor
  xdg-desktop-portal-impl
  xorg-xwayland
)
makedepends=(
  clang
  git
  rust
)
optdepends=(
  'alacritty: suggested GPU-accelerated terminal emulator'
  'bash: for the niri-session script'
  'fuzzel: suggested Wayland application launcher'
  'mako: suggested Wayland notification daemon'
  'org.freedesktop.secrets: for applications that use the secrets portal'
  'swaybg: suggested Wayland wallpaper tool'
  'swaylock: suggested Wayland screen locker'
  'waybar: suggested customizable Wayland bar'
  'xdg-desktop-portal-gtk: suggested XDG desktop portal'
  'xdg-desktop-portal-gnome: XDG desktop portal required for screencasting'
)
provides=('niri' 'xwayland-satellite' 'wayland-compositor')
conflicts=('niri' 'xwayland-satellite')
_niri_commit=6e40b2e2b5b278ca2c400b34ec7836c2dbbc8fd4
_satellite_commit=116451567841f8ff410863e22f3904c1ba0b69b1
source=(
  "niri::git+https://github.com/sidgwick/niri.git#commit=$_niri_commit"
  "xwayland-satellite::git+https://github.com/sidgwick/xwayland-satellite.git#commit=$_satellite_commit"
)
sha256sums=('SKIP' 'SKIP')

prepare() {
  cd "$srcdir/niri"
  cargo fetch --locked --target "$(rustc --print host-tuple)"

  cd "$srcdir/xwayland-satellite"
  sed -i 's|/usr/local/bin/xwayland-satellite|/usr/bin/xwayland-satellite|' \
    resources/xwayland-satellite.service
  cargo fetch --locked --target "$(rustc --print host-tuple)"
}

build() {
  cd "$srcdir/niri"
  export NIRI_BUILD_COMMIT="$(git rev-parse --short HEAD)"
  CFLAGS+=' -ffat-lto-objects'
  cargo build --frozen --release --features default

  for shell in bash fish zsh; do
    cargo run --frozen --release --bin niri -- \
      completions "$shell" > "$shell-completions"
  done

  cd "$srcdir/xwayland-satellite"
  cargo build --frozen --release --features systemd
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
  install -Dm644 resources/niri-portals.conf \
    "$pkgdir/usr/share/xdg-desktop-portal/niri-portals.conf"
  install -Dm644 resources/default-config.kdl README.md \
    -t "$pkgdir/usr/share/doc/niri/"
  install -Dm644 bash-completions \
    "$pkgdir/usr/share/bash-completion/completions/niri"
  install -Dm644 fish-completions \
    "$pkgdir/usr/share/fish/vendor_completions.d/niri.fish"
  install -Dm644 zsh-completions \
    "$pkgdir/usr/share/zsh/site-functions/_niri"
  install -Dm644 LICENSE "$pkgdir/usr/share/licenses/niri/LICENSE"

  cd "$srcdir/xwayland-satellite"
  install -Dm755 target/release/xwayland-satellite \
    "$pkgdir/usr/bin/xwayland-satellite"
  install -Dm644 resources/xwayland-satellite.service \
    "$pkgdir/usr/lib/systemd/user/xwayland-satellite.service"
  install -Dm644 LICENSE \
    "$pkgdir/usr/share/licenses/xwayland-satellite/LICENSE"
}
