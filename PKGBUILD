# Maintainer: Jan Alexander Steffens (heftig) <heftig@archlinux.org>
# Maintainer: Fabian Bornschein <fabiscafe@archlinux.org>
# Contributor: Ionut Biru <ibiru@archlinux.org>

pkgbase=vte3
pkgname=(
  vte-common
  vte3
  vte3-utils
  vte4
  vte4-utils
  vte-docs
)
pkgver=0.84.1
pkgrel=1
pkgdesc="Virtual Terminal Emulator widget"
url="https://gitlab.gnome.org/GNOME/vte"
arch=(x86_64)
license=(
  # Library
  LGPL-3.0-or-later

  # Demo app, some supporting files
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
  gi-docgen
  git
  glib2-devel
  gobject-introspection
  gperf
  graphene
  gtk3
  gtk4
  meson
  vala
)
options=(!lto)
source=("git+https://gitlab.gnome.org/GNOME/vte.git#tag=$pkgver"
        # Kitty keyboard protocol (CSI u) support, from
        # https://gitlab.gnome.org/GNOME/vte/-/merge_requests/14 (author: unxed),
        # rebased onto $pkgver. Lets apps that request it (e.g. opencode) see
        # modified keys such as Shift+Enter and Ctrl+Enter.
        0001-kitty-keyboard-protocol.patch)
b2sums=('f8f03ea82a08697d28a8e7a76729c39cb2088335380eb21b0af54f04519fb293f257a334c8b41948707b50cebbb8bb9c792a2c745b38f3834c9351ae730610ce'
        'f29baf6657ee4bc1eb60c3181654f229f02ffe9495bebfd1217aa4b3925fdea93ef5b30b1bbfe9939b0938d2503dde6cce97f8ca204951149f093eda8908893a')

prepare() {
  cd vte
  patch -Np1 -i "$srcdir/0001-kitty-keyboard-protocol.patch"
}

build() {
  local meson_options=(
    -D docs=true
  )

  arch-meson vte build "${meson_options[@]}"
  meson compile -C build
}

check() {
  meson test -C build --print-errorlogs
}

_pick() {
  local p="$1" f d; shift
  for f; do
    d="$srcdir/$p/${f#$pkgdir/}"
    mkdir -p "$(dirname "$d")"
    mv "$f" "$d"
    rmdir -p --ignore-fail-on-non-empty "$(dirname "$f")"
  done
}

package_vte-common() {
  pkgdesc+=" (common files)"
  depends=(glibc)

  meson install -C build --destdir "$pkgdir"

  cd "$pkgdir"

  _pick vte3 usr/include/vte-2.91
  _pick vte3 usr/lib/libvte-2.91.so*
  _pick vte3 usr/lib/pkgconfig/vte-2.91.pc
  _pick vte3 usr/lib/girepository-1.0/Vte-2.91.typelib
  _pick vte3 usr/share/gir-1.0/Vte-2.91.gir
  _pick vte3 usr/share/glade
  _pick vte3 usr/share/vala/vapi/vte-2.91.{deps,vapi}

  _pick vte3-utils usr/bin/vte-2.91
  _pick vte3-utils usr/share/applications/org.gnome.Vte.App.Gtk3.desktop
  _pick vte3-utils usr/share/xdg-terminals/org.gnome.Vte.App.Gtk3.desktop

  _pick vte4 usr/include/vte-2.91-gtk4
  _pick vte4 usr/lib/libvte-2.91-gtk4.so*
  _pick vte4 usr/lib/pkgconfig/vte-2.91-gtk4.pc
  _pick vte4 usr/lib/girepository-1.0/Vte-3.91.typelib
  _pick vte4 usr/share/gir-1.0/Vte-3.91.gir
  _pick vte4 usr/share/vala/vapi/vte-2.91-gtk4.{deps,vapi}

  _pick vte4-utils usr/bin/vte-2.91-gtk4
  _pick vte4-utils usr/share/applications/org.gnome.Vte.App.Gtk4.desktop
  _pick vte4-utils usr/share/xdg-terminals/org.gnome.Vte.App.Gtk4.desktop

  _pick docs usr/share/doc
}


package_vte3() {
  pkgdesc+=" (GTK3)"
  license=(LGPL-3.0-or-later)
  depends+=(
    at-spi2-core
    gtk3
    vte-common
  )
  provides+=(libvte-2.91.so)

  mv vte3/* "$pkgdir"
}

package_vte3-utils() {
  pkgdesc="VTE3 test utilities"
  license=(GPL-3.0-or-later)
  depends=(
    cairo
    gdk-pixbuf2
    glib2
    glibc
    gtk3
    libgcc
    libstdc++
    pango
    vte3
  )

  mv vte3-utils/* "$pkgdir"
}

package_vte4() {
  pkgdesc+=" (GTK4)"
  license=(LGPL-3.0-or-later)
  depends+=(
    graphene
    gtk4
    vte-common
  )
  provides+=(libvte-2.91-gtk4.so)

  mv vte4/* "$pkgdir"
}

package_vte4-utils() {
  pkgdesc="VTE4 test utilities"
  license=(GPL-3.0-or-later)
  depends=(
    cairo
    gdk-pixbuf2
    glib2
    glibc
    gtk4
    libgcc
    libstdc++
    pango
    vte4
  )

  mv vte4-utils/* "$pkgdir"
}

package_vte-docs() {
  pkgdesc+=" (documentation)"
  depends=()

  mv docs/* "$pkgdir"
}

# vim:set sw=2 sts=-1 et:
