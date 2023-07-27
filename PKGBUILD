# Maintainer: Sefa Eyeoglu <conctact@scrumplex.net>
# Contributor: dada513 <dada513@protonmail.com>
# Contributor: lordpipe <lordpipe@protonmail.com>

pkgname=prismlauncher
pkgver=7.2
pkgrel=2
pkgdesc="Minecraft launcher with ability to manage multiple instances."
arch=('i686' 'amd64' 'arm64' 'armhf' 'riscv64')
url="https://prismlauncher.org"
license=('GPL3')
depends=('libqt5svg5' 'qt5-image-formats-plugins' 'libqt5xml5' 'libqt5core5a' 'libqt5network5' 'libqt5gui5')
makedepends=('scdoc' 'extra-cmake-modules' 'cmake' 'git' 'openjdk-17-jdk' 'zlib1g-dev' 'libgl1-mesa-dev' 'qtbase5-dev' 'qtchooser' 'qt5-qmake' 'qtbase5-dev-tools' 'gcc' 'g++')
optdepends=('java-runtime=8: support for Minecraft versions < 1.17'
            'java-runtime=17: support for Minecraft versions >= 1.17')
source=("https://github.com/PrismLauncher/PrismLauncher/releases/download/$pkgver/PrismLauncher-$pkgver.tar.gz")
sha256sums=('5733b55c4532286813a6fb7de2f3a38e6f6db743a251919c8b646d32a84514b4')

# allow for ARM support
#TODO: makedeb's hard-coding for x86-64 has been fixed in a future makedeb version
#TODO: these 6 lines make this script match the behavior of future makedeb. When it releases, remove this
CARCH="$(uname -p)"
CHOST="$(uname -p)-pc-linux-gnu"
CFLAGS=${CFLAGS/-march=x86-64/}
CXXFLAGS=${CXXFLAGS/-march=x86-64/}
CFLAGS=${CFLAGS/-mtune=generic/}
CXXFLAGS=${CXXFLAGS/-mtune=generic/}

# if the user hasn't specified a tuning/architecture, specify our own minimal defaults to cover the earliest CPUs
if [[ ${CFLAGS} != *"-mtune"* && ${CFLAGS} != *"-march"* ]]; then
  case $(uname -m) in
    x86_64)
      CFLAGS+=" -march=x86-64 -mtune=generic"
      CXXFLAGS+=" -march=x86-64 -mtune=generic"
      ;;
    aarch64*|armv8*|armv9*|arm64*)
      CFLAGS+=" -march=armv8-a -mtune=generic"
      CXXFLAGS+=" -march=armv8-a -mtune=generic"
      ;;
    arm*)
      CFLAGS+=" -march=armv7-a -mtune=generic"
      CXXFLAGS+=" -march=armv7-a -mtune=generic"
      ;;
    riscv64*)
      CFLAGS+=" -march=rv64imafdc"
      CXXFLAGS+=" -march=rv64imafdc"
      CFLAGS=${CFLAGS/-fcf-protection/}
      CXXFLAGS=${CXXFLAGS/-fcf-protection/}
      ;;
  esac
fi

build() {
  cd "${srcdir}/PrismLauncher-$pkgver"
  cmake -DCMAKE_BUILD_TYPE=None \
    -DCMAKE_INSTALL_PREFIX="/usr" \
    -DLauncher_BUILD_PLATFORM="debian" \
    -DLauncher_APP_BINARY_NAME="${pkgname}" \
    -DLauncher_QT_VERSION_MAJOR=5 \
    -DENABLE_LTO=ON \
    -Bbuild -S.
  cmake --build build
}

check() {
  cd "${srcdir}/PrismLauncher-$pkgver/build"
  ctest . -E Task  # Skip unreliable Task test
}

package() {
  cd "${srcdir}/PrismLauncher-$pkgver/build"
  DESTDIR="$pkgdir" cmake --install .
}
