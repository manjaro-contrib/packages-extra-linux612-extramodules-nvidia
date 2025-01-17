# Maintainer: Bernhard Landauer <bernhard@manjaro.org>
# Maintainer: Philip Müller <philm[at]manjaro[dot]org>
# Contributor: Sven-Hendrik Haase <svenstaro@archlinux.org>
# Contributor: Felix Yan <felixonmars@archlinux.org>
# Contributor: Thomas Baechler <thomas@archlinux.org>

_linuxprefix=linux612

pkgbase="${_linuxprefix}-nvidia"
pkgname=("${_linuxprefix}-nvidia" "${_linuxprefix}-nvidia-open")
pkgdesc="NVIDIA drivers for ${_linuxprefix}"
pkgver=565.77
pkgrel=11
arch=('x86_64')
url="https://www.nvidia.com/"
license=('custom')
groups=("${_linuxprefix}-extramodules")
depends=("${_linuxprefix}" "nvidia-utils=${pkgver}")
makedepends=("${_linuxprefix}-headers")
provides=("nvidia=${pkgver}" 'NVIDIA-MODULE')
options=(!strip)
_durl="https://us.download.nvidia.com/XFree86/Linux-x86"
source=("${_durl}_64/${pkgver}/NVIDIA-Linux-x86_64-${pkgver}-no-compat32.run"
        'make-modeset-fbdev-default.patch')
sha256sums=('bdf6373649a3abea3355b95b9df2eedb52c97e2e2b2b14e60bf759551a1b5d7c'
            '1850b14877a87083d1800d0e75714347fc8049a0f4ff1e354769f3058e372e1a')

_pkg="NVIDIA-Linux-x86_64-${pkgver}-no-compat32"

prepare() {
    sh "${_pkg}.run" --extract-only

    cd "${_pkg}"

    # Enable modeset and fbdev as default
    # This avoids various issue, when Simplefb is used
    # https://gitlab.archlinux.org/archlinux/packaging/packages/nvidia-utils/-/issues/14
    # https://github.com/rpmfusion/nvidia-kmod/blob/master/make_modeset_default.patch
    patch -Np1 < "$srcdir"/make-modeset-fbdev-default.patch -d "${srcdir}/${_pkg}/kernel-open"
    patch -Np1 < "$srcdir"/make-modeset-fbdev-default.patch -d "${srcdir}/${_pkg}/kernel"
}

build() {
    _kernver="$(cat /usr/src/${_linuxprefix}/version)"

    cd "${_pkg}"
    make -C kernel-open SYSSRC=/usr/lib/modules/"${_kernver}/build" module
    make -C kernel SYSSRC=/usr/lib/modules/"${_kernver}/build" module
}

package_linux612-nvidia() {
    _kernver="$(cat /usr/src/${_linuxprefix}/version)"

    cd "${_pkg}"
    install -Dm 644 kernel/*.ko -t "${pkgdir}/usr/lib/modules/${_kernver}/extramodules/"

    # compress each module individually
    find "${pkgdir}" -name '*.ko' -exec zstd --rm -19 {} +

    install -Dm 644 LICENSE -t "${pkgdir}/usr/share/licenses/${pkgname}/"
}

package_linux612-nvidia-open() {
    pkgdesc="NVIDIA open drivers for ${_linuxprefix}"
    provides=("nvidia-open=${pkgver}" 'NVIDIA-MODULE')
    conflicts=("${_linuxprefix}-nvidia")

    _kernver="$(cat /usr/src/${_linuxprefix}/version)"

    cd "${_pkg}"
    install -Dm 644 kernel-open/*.ko -t "${pkgdir}/usr/lib/modules/${_kernver}/extramodules/"

    # compress each module individually
    find "${pkgdir}" -name '*.ko' -exec zstd --rm -19 {} +

    install -Dm 644 LICENSE -t "${pkgdir}/usr/share/licenses/${pkgname}/"
}
