# Maintainer: Levente Polyak <anthraxx[at]archlinux[dot]org>
# Contributor: Daniel Micay <danielmicay@gmail.com>
# Contributor: Tobias Powalowski <tpowa@archlinux.org>
# Contributor: Thomas Baechler <thomas@archlinux.org>

pkgbase=linux-hardened
pkgname=${pkgbase}-xps9560-tpm_tis
pkgver=5.18.9.hardened1
pkgrel=1
pkgdesc='Patched Security-Hardened Linux tpm_tis module for Dell XPS 9560'
url='https://github.com/anthraxx/linux-hardened'
arch=(x86_64)
license=(GPL2)
makedepends=(
  bc libelf pahole cpio perl tar xz
  xmlto python-sphinx python-sphinx_rtd_theme graphviz imagemagick texlive-latexextra
  git
)
options=('!strip')
_srcname=linux-${pkgver%.*}
_srctag=${pkgver%.*}-${pkgver##*.}

source=(
  https://www.kernel.org/pub/linux/kernel/v${pkgver%%.*}.x/${_srcname}.tar.{xz,sign}
  https://github.com/anthraxx/${pkgbase}/releases/download/${_srctag}/${pkgbase}-${_srctag}.patch{,.sig}
  config         # the main kernel config file
  revert-request_locality_before_TPM_INT_ENABLE.patch
)
validpgpkeys=(
  'ABAF11C65A2970B130ABE3C479BE3E4300411886'  # Linus Torvalds
  '647F28654894E3BD457199BE38DBBDC86092693E'  # Greg Kroah-Hartman
  'E240B57E2C4630BA768E2F26FC1B547C8D8172C8'  # Levente Polyak
)
sha256sums=('3882e26fcedcfe3ccfc158b9be2d95df25f26c3795ecf1ad95708ed532f5c93c'
            'SKIP'
            '65980147ccc17e105e3e02b4290397a221a4816f41a7ddfd99c723253250caaa'
            'SKIP'
            'c1bff23b41e9b16bc58506a2557dbf2fc19cf2b428b0d3ca21446d14acd4f29d'
            '8fe7b6d5a14aa8f7756818ee3862b539f55fd32581255e99b40e3498586b73ec')

export KBUILD_BUILD_HOST=archlinux
export KBUILD_BUILD_USER=$pkgbase
export KBUILD_BUILD_TIMESTAMP="$(date -Ru${SOURCE_DATE_EPOCH:+d @$SOURCE_DATE_EPOCH})"

prepare() {
  cd $_srcname

  echo "Setting version..."
  scripts/setlocalversion --save-scmversion
  echo "-$pkgrel" > localversion.10-pkgrel
  echo "${pkgbase#linux}" > localversion.20-pkgname

  local src
  for src in "${source[@]}"; do
    src="${src%%::*}"
    src="${src##*/}"
    [[ $src = *.patch ]] || continue
    echo "Applying patch $src..."
    patch -Np1 < "../$src"
  done

  echo "Setting config..."
  cp ../config .config
  make olddefconfig
  diff -u ../config .config || :

  make -s kernelrelease > version

  make modules_prepare
  echo "Prepared $pkgbase version $(<version)"
}

build() {
  cd $_srcname

  make M=drivers/char/tpm tpm_tis_core.ko
}

package() {
  cd $_srcname
  local kernver="$(<version)"
  # Place the tpm_tis module in updates to overload prebuilt module

  echo "Installing tpm_tis module..."
  make M=drivers/char/tpm \
    INSTALL_MOD_PATH="$pkgdir/usr" INSTALL_MOD_DIR="updates" \
    INSTALL_MOD_STRIP=1 \
    DEPMOD=/doesnt/exist modules_install  # Suppress depmod
}

# vim:set ts=8 sts=2 sw=2 et:
