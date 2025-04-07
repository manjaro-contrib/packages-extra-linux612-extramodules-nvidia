# Maintainer: Bernhard Landauer <bernhard@manjaro.org>
# Maintainer: Philip Müller <philm[at]manjaro[dot]org>
# Contributor: Sven-Hendrik Haase <svenstaro@archlinux.org>
# Contributor: Felix Yan <felixonmars@archlinux.org>
# Contributor: Thomas Baechler <thomas@archlinux.org>

_linuxprefix=linux612

pkgbase="${_linuxprefix}-nvidia"
pkgname=("${_linuxprefix}-nvidia" "${_linuxprefix}-nvidia-open")
pkgdesc="NVIDIA drivers for ${_linuxprefix}"
pkgver=570.133.07
pkgrel=8
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
        '0001-Enable-atomic-kernel-modesetting-by-default.patch')
sha256sums=('deaf8b334ad76e9f25f6a9fddff2633a74aa716965bd6c979d6f024e974d6f99'
            '84ec3947f8ff355d7fb26ab1cd0bf38c23b1e9c6eccf9fa7b51b74f2e66cdfa7')

_pkg="NVIDIA-Linux-x86_64-${pkgver}-no-compat32"

prepare() {
    sh "${_pkg}.run" --extract-only

    cd "${_pkg}"

    # Enable modeset by default
    # This avoids various issue, when Simplefb is used
    # https://gitlab.archlinux.org/archlinux/packaging/packages/nvidia-utils/-/issues/14
    # https://github.com/rpmfusion/nvidia-kmod/blob/master/make_modeset_default.patch
    patch -Np1 < "$srcdir"/0001-Enable-atomic-kernel-modesetting-by-default.patch -d "${srcdir}/${_pkg}/kernel-open"
    patch -Np1 < "$srcdir"/0001-Enable-atomic-kernel-modesetting-by-default.patch -d "${srcdir}/${_pkg}/kernel"
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
