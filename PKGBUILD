# Maintainer: phenylshima <phenylshima at outlook dot com>
# Contributor: ponsfoot <cabezon dot hashimoto at gmail dot com>

## NOTE: This doens't support kill_line feature of uim.

# renovate: aur-sync depName=mozc 6b642d11cc9aacc1eceafed305011bdea343ab59

_uimmozcrev="7beac7ba000e0459a4dc933f3873b521664d2665"
_mozcrev="5e6abfe1853b080766def432b746a9bed79e54b0"

pkgname=uim-mozc
_pkgname=mozc
pkgver=2.30.5544.102
pkgrel=1
pkgdesc="uim plugin module for Mozc"
arch=('i686' 'x86_64')
url="https://github.com/e-kato/macuim"
license=('BSD')
groups=('mozc-im')
depends=('mozc>=2.30.5544.102' 'uim')
install=${pkgname}.install
makedepends=('bazel' 'git' 'python')
source=(
  mozc::git+https://github.com/google/mozc.git#commit=${_mozcrev}
  uim-mozc::git+https://github.com/e-kato/macuim.git#commit=${_uimmozcrev}
  'bazel.patch'
  'mozc.patch'
  'BUILD.bazel'
)
sha1sums=('SKIP'
          'SKIP'
          '7547e5cae4df8b516580c882bc975d8a70251db1'
          'eff91b1139561bb5e2f90936776b8e796e7506a7'
          '279a05a3e6339c388744f16952ccd798ab9989a8')

prepare() {
  cd "${srcdir}/${_pkgname}/"

  git submodule update --init --recursive
  patch -p1 -i "${srcdir}/bazel.patch"

  cd "${srcdir}/${_pkgname}/src"

  cp -rf "${srcdir}/uim-mozc/Mozc/uim" unix/
  patch -p1 -i "${srcdir}/mozc.patch"

  cp -rf "${srcdir}/BUILD.bazel" unix/uim

  # Extract license part of uim-mozc
  head -n 32 unix/uim/mozc.cc > unix/uim/LICENSE
}


build() {
  cd "${srcdir}/${_pkgname}/src"

  msg "Starting make..."

  unset ANDROID_NDK_HOME
  export JAVA_HOME='/usr/lib/jvm/java-11-openjdk/'
  bazel build unix/uim:uim-mozc unix/icons --config oss_linux --compilation_mode opt --cxxopt=-Wno-uninitialized --host_cxxopt=-Wno-uninitialized --experimental_cc_shared_library
}

package() {
  cd "${srcdir}/${_pkgname}/src"
  install -D -m 755 bazel-bin/unix/uim/libuim-mozc.so "${pkgdir}/usr/lib/uim/plugin/libuim-mozc.so"
  install -d "${pkgdir}/usr/share/uim"
  install    -m 644 ${srcdir}/uim-mozc/Mozc/scm/*.scm "${pkgdir}/usr/share/uim/"
  install -D -m 644 data/images/unix/ime_product_icon_opensource-32.png "${pkgdir}/usr/share/uim/pixmaps/mozc.png"
  install    -m 644 data/images/unix/ui-tool.png "${pkgdir}/usr/share/uim/pixmaps/mozc_tool_selector.png"
  install    -m 644 data/images/unix/ui-properties.png "${pkgdir}/usr/share/uim/pixmaps/mozc_tool_config_dialog.png"
  install    -m 644 data/images/unix/ui-dictionary.png "${pkgdir}/usr/share/uim/pixmaps/mozc_tool_dictionary_tool.png"

  install -D -m 644 unix/uim/LICENSE "${pkgdir}/usr/share/licenses/${_pkgname}/uim-mozc/LICENSE"
}
