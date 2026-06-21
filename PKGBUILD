# Maintainer: Bernhard Landauer <bernhard@manjaro.org>
# Maintainer: Philip Müller <philm[at]manjaro[dot]org>
# Contributor: Christian Hesse <eworm@archlinux.org>
# Contributor: Sébastien "Seblu" Luttringer
# Contributor: Ionut Biru <ibiru@archlinux.org>

_linuxprefix=linux71

pkgname=("${_linuxprefix}-virtualbox-host-modules")
pkgver=7.2.10
_pkgver="${pkgver}_OSE"
pkgrel=4
pkgdesc='Virtualbox host kernel modules for Manjaro Kernel'
arch=('x86_64')
url='http://virtualbox.org'
license=('GPL')
groups=("${_linuxprefix}-extramodules")
depends=("${_linuxprefix}")
makedepends=("${_linuxprefix}-headers" "virtualbox-host-dkms=$pkgver")
provides=('VIRTUALBOX-HOST-MODULES')
conflicts=("${_linuxprefix}-virtualbox-modules" 'virtualbox-host-dkms')
replaces=("${_linuxprefix}-virtualbox-modules")
sha256sums=()

prepare() {
  return 0
}

build() {
  _kernver="$(cat /usr/src/${_linuxprefix}/version)"

  fakeroot dkms build --dkmstree "$srcdir" -m vboxhost/${pkgver}_OSE -k ${_kernver}
}

package() {
  _kernver="$(cat /usr/src/${_linuxprefix}/version)"

  cd "vboxhost/${pkgver}_OSE/${_kernver}/$CARCH/module"
  install -Dm 644 *.ko* -t "$pkgdir/usr/lib/modules/${_kernver}/extramodules/"

  # compress each module individually
  find "${pkgdir}" -name '*.ko' -exec zstd --rm -19 {} +

  # systemd module loading
  printf '%s\n' vboxdrv vboxnetadp vboxnetflt |
  install -Dm 644 /dev/stdin "$pkgdir/usr/lib/modules-load.d/$pkgname.conf"
}
