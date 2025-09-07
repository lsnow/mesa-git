# Maintainer: Reza Jahanbakhshi <reza.jahanbakhshi at gmail dot com
# Contributor: Lone_Wolf <lone_wolf@klaas-de-kat.nl>
# Contributor: Armin K. <krejzi at email dot com>
# Contributor: Kristian Klausen <klausenbusk@hotmail.com>
# Contributor: Egon Ashrafinia <e.ashrafinia@gmail.com>
# Contributor: Tavian Barnes <tavianator@gmail.com>
# Contributor: Jan de Groot <jgc@archlinux.org>
# Contributor: Andreas Radke <andyrtr@archlinux.org>
# Contributor: Thomas Dziedzic < gostrc at gmail >
# Contributor: Antti "Tera" Oja <antti.bofh@gmail.com>
# Contributor: Diego Jose <diegoxter1006@gmail.com>

pkgname=mesa-git
pkgdesc="an open-source implementation of the OpenGL specification, git version"
pkgver=25.3.0_devel.65004.62815cc91f
pkgrel=1
arch=('x86_64')
makedepends=('git' 'python-mako' 'xorgproto' 'libxml2' 'libvdpau' 'libva' 'elfutils' 'libxrandr'
                            'wayland-protocols' 'meson' 'ninja' 'glslang'
)
depends=('libdrm' 'libxxf86vm' 'libxdamage' 'libxshmfence' 'libelf'
         'libomxil-bellagio' 'libunwind' 'libglvnd' 'wayland' 'lm_sensors' 
         'vulkan-icd-loader' 'zstd' 'expat' 'gcc-libs' 'libxfixes' 'libx11' 'systemd-libs' 'libxext' 'libxcb'
         'glibc' 'zlib'
)
optdepends=('opengl-man-pages: for the OpenGL API man pages')
provides=('mesa' 'vulkan-intel' 'vulkan-radeon' 'vulkan-mesa-layers' 'libva-mesa-driver' 'mesa-vdpau' 'vulkan-swrast' 'vulkan-driver' 'mesa-libgl' 'opengl-driver')
conflicts=('mesa' 'opencl-clover-mesa' 'opencl-rusticl-mesa' 'vulkan-intel' 'vulkan-radeon' 'vulkan-mesa-layers' 'libva-mesa-driver' 'mesa-vdpau' 'vulkan-swrast' 'mesa-libgl')
url="https://www.mesa3d.org"
license=('custom')
#source=('mesa::git+https://gitlab.freedesktop.org/mesa/mesa.git#branch=main'
#             'LICENSE'
#)
mesa_dir="${HOME}/abs/mesa"
source=(
    'LICENSE'
)

md5sums=(
    '5c65a0fe315dd347e09b1f2826a1df5a'
)
sha512sums=(
    '25da77914dded10c1f432ebcbf29941124138824ceecaf1367b3deedafaecabc082d463abcfa3d15abff59f177491472b505bcb5ba0c4a51bb6b93b4721a23c2'
)

options=(!lto debug strip)
#options=(!lto strip)
# lto and debug are disabled manually through meson -D flags, but it feels cleaner to also list them here.


# NINJAFLAGS is an env var used to pass commandline options to ninja
# NOTE: It's your responbility to validate the value of $NINJAFLAGS. If unsure, don't set it.

pkgver() {
    cd ${mesa_dir}
    local _ver
    _ver=$(<VERSION)

    echo ${_ver/-/_}.$(git rev-list --count HEAD).$(git rev-parse --short HEAD)
}

build_dir="${mesa_dir}/_build"
prepare() {
    # although removing _build folder in build() function feels more natural,
    # that interferes with the spirit of makepkg --noextract
    if [  -d ${build_dir} ]; then
        rm -rf ${build_dir}
    fi
}

build () {
    cd ${mesa_dir}
    meson setup \
       -D b_lto=false \
       -D platforms=x11,wayland \
       -D gallium-drivers=virgl,zink \
       -D vulkan-drivers=amd \
       -D vulkan-layers=device-select,overlay \
       -D egl=enabled \
       -D gallium-extra-hud=true \
       -D gallium-va=enabled \
       -D gallium-vdpau=enabled \
       -D gbm=enabled \
       -D gles1=disabled \
       -D gles2=enabled \
       -D glvnd=enabled \
       -D glx=dri \
       -D vulkan-beta=true \
       -D libunwind=enabled \
       -D llvm=disabled \
       -D lmsensors=enabled \
       -D microsoft-clc=disabled \
       -D valgrind=disabled \
       -D tools=[] \
       -D zstd=enabled \
       -D video-codecs=av1dec,av1enc,h264dec,h265dec,h264enc,h264dec \
       -D prefix=/usr \
       -D sysconfdir=/etc \
       -D b_ndebug=false \
       -D sysprof=true \
       --optimization=g \
       _build
       #--wrap-mode=nofallback \
       #-D buildtype=debug \
       
    meson configure --no-pager _build
    
    ninja $NINJAFLAGS -C _build
}

package() {
    cd ${mesa_dir}
    DESTDIR="${pkgdir}" ninja $NINJAFLAGS -C _build install

    # remove script file from /usr/bin
    # https://gitlab.freedesktop.org/mesa/mesa/issues/2230
    rm "${pkgdir}/usr/bin/mesa-overlay-control.py"
    rmdir "${pkgdir}/usr/bin"

    # indirect rendering
    ln -s /usr/lib/libGLX_mesa.so.0 "${pkgdir}/usr/lib/libGLX_indirect.so.0"
  
    install -m644 -Dt "${pkgdir}/usr/share/licenses/${pkgname}" "${srcdir}/LICENSE"
}
