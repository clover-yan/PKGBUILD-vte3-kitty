# Maintainer: Clover Yan <i@cloveryan dot com>
# Contributor: Jan Alexander Steffens (heftig) <heftig@archlinux.org>
# Contributor: Fabian Bornschein <fabiscafe@archlinux.org>
# Contributor: Ionut Biru <ibiru@archlinux.org>

pkgbase=vte-kitty
pkgname=(
  vte3-kitty
  vte4-kitty
)
pkgver=0.84.1
pkgrel=1
pkgdesc="Virtual Terminal Emulator widget (kitty keyboard protocol patch)"
url="https://gitlab.gnome.org/GNOME/vte"
arch=(x86_64)
license=(
  LGPL-3.0-or-later
  GPL-3.0-or-later
)
depends=(
  cairo
  fribidi
  glib2
  glibc
  gnutls
  icu
  libgcc
  libstdc++
  libxkbcommon
  lz4
  pango
  pcre2
  simdutf
  systemd
  systemd-libs
)
makedepends=(
  at-spi2-core
  fast_float
  fmt
  git
  glib2-devel
  gobject-introspection
  gperf
  graphene
  gtk3
  gtk4
  meson
)
options=(!lto)
source=("git+https://gitlab.gnome.org/GNOME/vte.git#tag=$pkgver"
        # Kitty keyboard protocol (CSI u) support, from
        # https://gitlab.gnome.org/GNOME/vte/-/merge_requests/14 (author: unxed),
        # rebased onto $pkgver. Lets apps that request it (e.g. opencode) see
        # modified keys such as Shift+Enter and Ctrl+Enter.
        0001-kitty-keyboard-protocol.patch)
b2sums=('f8f03ea82a08697d28a8e7a76729c39cb2088335380eb21b0af54f04519fb293f257a334c8b41948707b50cebbb8bb9c792a2c745b38f3834c9351ae730610ce'
        'a01542f50debf1c7ec3bd3cbf9865c32a5de05523c3065ad4fce96c3101f7904df249ca68dc0663f5437673c32de3a753d03022f88192ebfcf3fdb324e762017')

prepare() {
  cd vte
  patch -Np1 -i "$srcdir/0001-kitty-keyboard-protocol.patch"
}

build() {
  arch-meson vte build \
    -D gtk3=true \
    -D gtk4=true \
    -D app=false \
    -D docs=false \
    -D gir=true \
    -D vapi=false \
    -D glade=false
  meson compile -C build
}

check() {
  meson test -C build --print-errorlogs
}

_install_and_prune() {
  meson install -C build --destdir "$pkgdir"
  cd "$pkgdir"

  # Shared data, shell integration and utilities are provided by the
  # official vte-common package.
  rm -rf etc usr/bin usr/libexec usr/etc usr/lib/systemd \
         usr/lib/vte-urlencode-cwd \
         usr/share/applications usr/share/xdg-terminals usr/share/doc \
         usr/share/locale usr/share/vte-2.91 usr/share/glade usr/share/vala
  find . -type d -empty -delete
}

package_vte3-kitty() {
  pkgdesc+=" (GTK3)"
  depends+=(at-spi2-core gtk3 vte-common)
  provides=('vte3' 'libvte-2.91.so')
  conflicts=('vte3')

  _install_and_prune

  rm -rf usr/include/vte-2.91-gtk4 \
         usr/lib/libvte-2.91-gtk4.so* \
         usr/lib/pkgconfig/vte-2.91-gtk4.pc \
         usr/lib/girepository-1.0/Vte-3.91.typelib \
         usr/share/gir-1.0/Vte-3.91.gir
  find . -type d -empty -delete
}

package_vte4-kitty() {
  pkgdesc+=" (GTK4)"
  depends+=(graphene gtk4 vte-common)
  provides=('vte4' 'libvte-2.91-gtk4.so')
  conflicts=('vte4')

  _install_and_prune

  rm -rf usr/include/vte-2.91 \
         usr/lib/libvte-2.91.so* \
         usr/lib/pkgconfig/vte-2.91.pc \
         usr/lib/girepository-1.0/Vte-2.91.typelib \
         usr/share/gir-1.0/Vte-2.91.gir
  find . -type d -empty -delete
}

# vim:set sw=2 sts=-1 et:
