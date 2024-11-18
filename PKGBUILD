# Maintainer: Bernhard Landauer <bernhard@manjaro.org>
# Maintainer: Philip Müller <philm[at]manjaro[dot]org>
# Archlinux maintainer : Thomas Baechler <thomas@archlinux.org>

_linuxprefix=linux612

pkgname="${_linuxprefix}-nvidia"
pkgdesc="NVIDIA drivers for linux"
pkgver=550.127.05
pkgrel=2
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
        nvidia-drm-Set-FOP_UNSIGNED_OFFSET-for-nv_drm_fops.f.patch)
sha256sums=('27343aa3eecf10e90c5abd6d4edfdcc5bfcc0a2b07eb90e41310f837d06a8fb7'
            '6b888e586818397525f23503da8b369cab96ed4bec0ae6aa3138fde7f87b9f89')

_pkg="NVIDIA-Linux-x86_64-${pkgver}-no-compat32"

prepare() {
    sh "${_pkg}.run" --extract-only
    cd "${_pkg}"

    # Patch by NVIDIA to fix the 6.12 Kernel opening the display
    # https://github.com/NVIDIA/open-gpu-kernel-modules/issues/712
    patch -Np1 < "$srcdir"/nvidia-drm-Set-FOP_UNSIGNED_OFFSET-for-nv_drm_fops.f.patch -d "${srcdir}/${_pkg}/kernel-open"
    patch -Np1 < "$srcdir"/nvidia-drm-Set-FOP_UNSIGNED_OFFSET-for-nv_drm_fops.f.patch -d "${srcdir}/${_pkg}/kernel"

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
