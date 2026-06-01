# Maintainer: Bernhard Landauer <bernhard@manjaro.org>
# Maintainer: Philip Müller <philm[at]manjaro[dot]org>
# Contributor: Christian Hesse <eworm@archlinux.org>
# Contributor: Sébastien "Seblu" Luttringer
# Contributor: Ionut Biru <ibiru@archlinux.org>

_linuxprefix=linux71

pkgname=("${_linuxprefix}-virtualbox-host-modules")
pkgver=7.2.8
_pkgver="${pkgver}_OSE"
pkgrel=0.3
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
source=('linux71.patch')
sha256sums=('5c84bbaed18d8c7e6b302f7110cafa33caed9b5345281cd320f67993037c27e0')

prepare() {
  mkdir -p vboxhost/${pkgver}_OSE/source_patched
  cp -av /usr/src/vboxhost-${pkgver}_OSE/* vboxhost/${pkgver}_OSE/source_patched
  cd vboxhost/${pkgver}_OSE/source_patched/vboxdrv
  patch -p5 -i $srcdir/linux71.patch
  cd ../..
  ln -sfv source_patched source
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
