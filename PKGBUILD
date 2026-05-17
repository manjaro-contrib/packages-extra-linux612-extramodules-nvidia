# Maintainer: Bernhard Landauer <bernhard@manjaro.org>
# Maintainer: Philip Müller <philm[at]manjaro[dot]org>
# Contributor: Sven-Hendrik Haase <svenstaro@archlinux.org>
# Contributor: Felix Yan <felixonmars@archlinux.org>
# Contributor: Thomas Baechler <thomas@archlinux.org>

_linuxprefix=linux612

pkgname="${_linuxprefix}-nvidia"
pkgver=595.71.05
pkgrel=8
pkgdesc="NVIDIA kernel modules for ${_linuxprefix}"
arch=('x86_64')
url="https://www.nvidia.com/"
license=('custom')
groups=("${_linuxprefix}-extramodules")
depends=("${_linuxprefix}" "nvidia-utils=${pkgver}" 'libglvnd')
makedepends=("${_linuxprefix}-headers" "nvidia-dkms=$pkgver")
provides=("nvidia=${pkgver}" 'NVIDIA-MODULE')
options=(!strip)

build() {
    _kernver="$(cat /usr/src/${_linuxprefix}/version)"

    fakeroot dkms build --dkmstree "${srcdir}" -m nvidia/${pkgver} -k ${_kernver}
}

package() {
    _kernver="$(cat /usr/src/${_linuxprefix}/version)"

    install -Dt "${pkgdir}/usr/lib/modules/${_kernver}/extramodules" -m644 nvidia/${pkgver}/${_kernver}/${CARCH}/module/*.ko.zst

    # compress each module individually
    find "$pkgdir" -name '*.ko' -exec zstd --rm -19 {} +

    install -Dm644 /usr/share/licenses/nvidia-dkms/LICENSE "${pkgdir}/usr/share/licenses/${pkgname}/LICENSE"
}
