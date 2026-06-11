# https://gitlab.archlinux.org/archlinux/packaging/packages/telegram-desktop
pkgname=telegram-desktop-no-ads
pkgver=6.9.1
_td_commit=51743dfd01dff6179e2d8f7095729caa4e2222e9
pkgrel=1
pkgdesc='Patched Telegram Desktop client without ads'
arch=('x86_64')
url="https://desktop.telegram.org/"
license=('GPL3')
depends=(
  'abseil-cpp'
  'ada'
  'ffmpeg'
  'glib2'
  'glibc'
  'hicolor-icon-theme'
  'hunspell'
  'kcoreaddons'
  'libavif'
  'libdispatch'
  'libgcc'
  'libheif'
  'libjxl'
  'libstdc++'
  'libxcomposite'
  'libxdamage'
  'libxrandr'
  'libxtst'
  'lz4'
  'minizip'
  'openal'
  'openh264'
  'openssl'
  'pipewire'
  'protobuf'
  'qt6-imageformats'
  'qt6-svg'
  'qt6-wayland'
  'rnnoise'
  'xxhash'
  'zlib'
)
makedepends=(
  'boost'
  'boost-libs'
  'cmake'
  'git'
  'glib2-devel'
  'gobject-introspection'
  'gperf'
  'libtg_owt'
  'microsoft-gsl'
  'ninja'
  'python'
  'range-v3'
  'tl-expected'
)
conflicts=("telegram-desktop")
# Patches are from feature/remove-ads branch:
# https://github.com/vehlwn/tdesktop/tree/feature/remove-ads
# git format-patch upstream/dev..feature/remove-ads --stdout > remove-ads.patch
source=(
    "https://github.com/telegramdesktop/tdesktop/releases/download/v${pkgver}/tdesktop-${pkgver}-full.tar.gz"
    "git+https://github.com/tdlib/td.git#tag=${_td_commit}"
    "remove-ads.patch"
)
sha256sums=(
    'SKIP'
    'SKIP'
    '52d44a0a42ad3ecae15fd7d5389573c5b35997d9e4a5ac42c2648264533d7335'
)

prepare() {
    cd tdesktop-$pkgver-full
    patch --forward --strip=1 -i "${srcdir}/remove-ads.patch"
    # Fix missing cstdint includes in tgcalls headers
    sed -i '/#include <memory>/a #include <cstdint>' Telegram/ThirdParty/tgcalls/tgcalls/DirectConnectionChannel.h
    sed -i '/#include <map>/a #include <cstdint>' Telegram/ThirdParty/tgcalls/tgcalls/Instance.h
    sed -i '/#include <vector>/a #include <cstdint>' Telegram/ThirdParty/tgcalls/tgcalls/v2/SignalingConnection.h
    sed -i '/#include "SignalingConnection.h"/a #include <cstdint>' Telegram/ThirdParty/tgcalls/tgcalls/v2/ExternalSignalingConnection.h
    sed -i '/#include <cstdlib>/a #include <cstdint>' Telegram/ThirdParty/tgcalls/tgcalls/third-party/json11.cpp

}

build() {
    cmake -S td -B td/build \
        -DCMAKE_BUILD_TYPE=None \
        -DCMAKE_INSTALL_PREFIX="$PWD/td/install" \
        -Wno-dev \
        -DTD_E2E_ONLY=ON
    cmake --build td/build
    cmake --install td/build

    cmake -B build -S tdesktop-$pkgver-full -G Ninja \
        -DCMAKE_INSTALL_PREFIX="/usr" \
        -Dtde2e_DIR="$PWD/td/install/lib/cmake/tde2e" \
        -DCMAKE_BUILD_TYPE=Release \
        -DTDESKTOP_API_ID=611335 \
        -DTDESKTOP_API_HASH=d524b414d21f4d37f08684c1df41ac9c
    cmake --build build
}

package() {
    DESTDIR="$pkgdir" cmake --install build
}
