# vte-kitty

Unofficial Arch Linux packaging of [VTE](https://gitlab.gnome.org/GNOME/vte) with the
[kitty keyboard protocol](https://sw.kovidgoyal.net/kitty/keyboard-protocol/) patch
applied, so applications can receive keys such as `Shift+Enter` and `Ctrl+Enter` as
distinct escape sequences (`CSI ... u`).

## Why

VTE (used by Ptyxis, GNOME Terminal, ...) collapses modified Enter into plain `\r`,
so `Shift+Enter` and `Ctrl+Enter` are indistinguishable from `Enter`. Applications
that enable the kitty keyboard protocol — [OpenCode](https://opencode.ai) for
example — expect `\x1b[13;2u` for `Shift+Enter`.

Upstream VTE does not implement this protocol yet, so this repository carries the
unmerged merge request
[!14](https://gitlab.gnome.org/GNOME/vte/-/merge_requests/14), rebased onto VTE
0.84.1.

## Packages

- `vte3-kitty` — GTK3 build, `conflicts`/`provides` `vte3`
- `vte4-kitty` — GTK4 build, `conflicts`/`provides` `vte4`

Shared data (`vte-common`) and the other VTE packages stay from the official
repositories.

Build and install:

```sh
makepkg -si
```

Installing replaces the corresponding official package (`vte3` or `vte4`). To go
back to the distribution packages:

```sh
sudo pacman -S vte3 vte4
```

## Behavior

The change is opt-in. The terminal only switches to `CSI-u` encoding after an
application enables the protocol (`CSI > flags u` / `CSI = flags ; mode u`).
Shells and applications that don't ask for it are unaffected.

## Known limitations

- The patch is from an **unmerged** upstream merge request; expect rough edges.
- Alternate-key/shifted-key reporting (protocol flags 4 and 16) uses a hardcoded
  US-layout table and can be inaccurate on non-US layouts. The main key code is
  unaffected.
- If an application crashes without popping the keyboard flags, the terminal stays
  in kitty mode (`Ctrl+C` becomes `CSI-u`). Run `reset`, or open a new tab. The
  protocol deliberately keeps `Enter`, `Tab` and `Backspace` in legacy encoding so
  that `reset` can still be typed.
- These packages have different names than the official ones, so `pacman -Syu`
  will **not** upgrade them. When VTE makes a new release, update `pkgver`, rebuild
  and reinstall — otherwise you stay on 0.84.1, including missing security fixes.

## Credits

The patch is from [VTE merge request !14](https://gitlab.gnome.org/GNOME/vte/-/merge_requests/14)
by **unxed**, applied on top of VTE 0.84.1 with duplicate `#include` lines removed.
VTE is licensed under LGPL-3.0-or-later. The packaging is based on the Arch Linux
`vte3` PKGBUILD.
