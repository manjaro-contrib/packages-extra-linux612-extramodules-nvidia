# Maintainer: Bernhard Landauer <bernhard@manjaro.org>
# Maintainer: Philip Müller <philm[at]manjaro[dot]org>
# Archlinux maintainer : Thomas Baechler <thomas@archlinux.org>

_linuxprefix=linux612

pkgname="${_linuxprefix}-nvidia"
pkgdesc="NVIDIA drivers for linux"
pkgver=565.57.01
pkgrel=0.4
arch=('x86_64')
url="http://www.nvidia.com/"
license=('custom')
groups=("${_linuxprefix}-extramodules")
depends=("${_linuxprefix}" "nvidia-utils=${pkgver}")
makedepends=("${_linuxprefix}-headers")
provides=("nvidia=${pkgver}" 'NVIDIA-MODULE')
options=(!strip)
_durl="https://us.download.nvidia.com/XFree86/Linux-x86"
source=("${_durl}_64/${pkgver}/NVIDIA-Linux-x86_64-${pkgver}-no-compat32.run"
        'kernel612.patch'
        'make-modeset-fbdev-default.patch')
sha256sums=('9ec280cf6544b59d170064e00c365f329c6f28416eba1497c286f4e0295c27ce'
            'f2f9c418da0c5c0bcee236ae1cace193baf460b15470702781faa31c128d9a92'
            '1850b14877a87083d1800d0e75714347fc8049a0f4ff1e354769f3058e372e1a')

_pkg="NVIDIA-Linux-x86_64-${pkgver}-no-compat32"

prepare() {
    sh "${_pkg}.run" --extract-only

    cd "${_pkg}"

    # Enable modeset and fbdev as default
    # This avoids various issue, when Simplefb is used
    # https://gitlab.archlinux.org/archlinux/packaging/packages/nvidia-utils/-/issues/14
    # https://github.com/rpmfusion/nvidia-kmod/blob/master/make_modeset_default.patch
    patch -Np1 < "$srcdir"/make-modeset-fbdev-default.patch -d "${srcdir}/${_pkg}/kernel"
    
    # patch open kernel module
    patch -Np1 -i ../kernel612.patch
    
    # patch proprietary kernel module
    cd kernel
    patch -Np2 -i ../../kernel612.patch
}

build() {
    _kernver="$(cat /usr/src/${_linuxprefix}/version)"

    cd "${_pkg}"
    make -C kernel SYSSRC=/usr/lib/modules/"${_kernver}/build" module
}

package() {
    _kernver="$(cat /usr/src/${_linuxprefix}/version)"

    cd "${_pkg}"
    install -Dm 644 kernel/*.ko -t "${pkgdir}/usr/lib/modules/${_kernver}/extramodules/"

    # compress each module individually
    find "${pkgdir}" -name '*.ko' -exec xz -T1 {} +

    install -Dm 644 LICENSE -t "${pkgdir}/usr/share/licenses/${pkgname}/"
}
