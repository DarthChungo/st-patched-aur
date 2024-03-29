# Maintainer: DarthChungo <antonio adaro eu>

# Based on st-git by:
# Maintainer:  Vincent Grande <shoober420@gmail.com>
# Contributor: Static_Rocket
# Contributor: Jose Riha <jose1711 gmail com>
# Contributor: Sebastian J. Bronner <waschtl@sbronner.com>
# Contributor: Patrick Jackson <PatrickSJackson gmail com>
# Contributor: Christoph Vigano <mail@cvigano.de>

pkgname=st-patched-git
pkgver=0.9+9846a56
pkgrel=1
pkgdesc='A simple virtual terminal emulator for X, with popular patches included.'
arch=('i686' 'x86_64' 'armv7h')
license=('MIT')
depends=(libxft)
makedepends=('ncurses' 'libxext' 'git')
provides=(st)
conflicts=(st)
url=https://github.com/DarthChungo/st-patched/
source=(st-patched::git+https://github.com/DarthChungo/st-patched.git
        terminfo.patch)
sha256sums=(SKIP
            SKIP)

_gitname="st-patched"
_sourcedir="$_gitname"
_patchdir="$_sourcedir/st"

pkgver() {
  cd "$_sourcedir"

  . ./version
  echo "$ver+$rev"
}

prepare() {
  ./$_sourcedir/patch.sh

  patch -d"$_patchdir" -p1 < terminfo.patch

  # This package provides a mechanism to provide a custom config.h. Multiple
  # configuration states are determined by the presence of two files in
  # $BUILDDIR:
  #
  # config.h  config.def.h  state
  # ========  ============  =====
  # absent    absent        Initial state. The user receives a message on how
  #                         to configure this package.
  # absent    preent        The user was previously made aware of the
  #                         configuration options and has not made any
  #                         configuration changes. The package is built using
  #                         default values.
  # present                 The user has supplied his or her configuration. The
  #                         file will be copied to $srcdir and used during
  #                         build.
  #
  # After this test, config.def.h is copied from $srcdir to $BUILDDIR to
  # provide an up to date template for the user.

  if [ -e "$BUILDDIR/config.h" ]; then
    cp "$BUILDDIR/config.h" "$_patchdir"

  elif [ ! -e "$BUILDDIR/config.def.h" ]; then
    local msg=''
    msg+='This package can be configured in config.h. Copy the config.def.h '
    msg+='that was just placed into the package directory to config.h and '
    msg+='modify it to change the configuration. Or just leave it alone to '
    msg+='continue to use default values.'

    printf "\033[33;1m==> WARNING: \033[37;1m%s\033[0m\n" "$msg"
  fi

  cp "$_patchdir/config.def.h" "$BUILDDIR"
}

build() {
  make -C "$_patchdir" X11INC=/usr/include/X11 X11LIB=/usr/lib/X11

  local msg=''
  msg+='See the st-patched README for info on updating your system'\''s st '
  msg+='terminfo entry if you wish to use the undercurl patch provided by '
  msg+='st-patched.'

  printf "\033[33;1m==> WARNING: \033[37;1m%s\033[0m\n" "$msg"
}

package() {
  local shrdir="$pkgdir/usr/share"

  make -C "$_patchdir" PREFIX=/usr DESTDIR="$pkgdir" install

  install -m 0644 -D -t "$shrdir/applications/" "$_patchdir/st.desktop"
  install -m 0644 -D -t "$shrdir/licenses/$_gitname" "$_sourcedir/LICENSE"
  install -m 0644 -D -t "$shrdir/doc/$_gitname" "$_sourcedir/README.md"
}
