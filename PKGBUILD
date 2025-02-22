# Contributor: graysky <graysky AT archlinux DOT us>
# Contributor: Jan Alexander Steffens (heftig) <jan.steffens@gmail.com>

### BUILD OPTIONS
# Any/all of the next three variables may be set to ANYTHING
# that is not null to enable their respective build options

# Tweak kernel options prior to a build via nconfig
_makenconfig=

# Only compile select modules to reduce the number of modules built
#
# To keep track of which modules are needed for your specific system/hardware,
# give module_db a try: https://aur.archlinux.org/packages/modprobed-db
# This PKGBUILD reads the database kept if it exists
# More at this wiki page ---> https://wiki.archlinux.org/index.php/Modprobed-db
_localmodcfg=

# Compile using clang rather than gcc
_clangbuild=

### IMPORTANT: Do no edit below this line unless you know what you're doing
pkgbase=linux-xck
pkgver=6.13.4
pkgrel=1
arch=(x86_64)
license=(GPL-2.0-only)
makedepends=(
  bc
  cpio
  gettext
  libelf
  pahole
  perl
  python
  tar
  xz
)
[[ -n "$_clangbuild" ]] && makedepends+=(clang llvm lld python)
options=(
  !debug
  !strip
)

# https://ck-hack.blogspot.com/2021/08/514-and-future-of-muqss-and-ck-once.html
# acknowledgment to xanmod for initially keeping the hrtimer patches up to date
_ckhrtimer=linux-6.11.y
_commit=7bdeefd29a299f812f1d14ef7ef46bdb32ed5b6d

_gcc_more_v=20241018
_sched_ext=bore-patches-v13
_bore=0001-linux6.13.y-bore5.9.6.patch
source=(
  "https://www.kernel.org/pub/linux/kernel/v6.x/linux-$pkgver.tar".{xz,sign}
  config  # the main kernel config file
  "more-uarches-$_gcc_more_v.tar.gz::https://github.com/graysky2/kernel_compiler_patch/archive/$_gcc_more_v.tar.gz"
  #"ck-hrtimer-$_commit.tar.gz::https://github.com/graysky2/linux-patches/archive/$_commit.tar.gz"
  #https://github.com/sirlucjan/kernel-patches/raw/master/6.12/$_sched_ext/$_bore
  https://github.com/firelzrd/bore-scheduler/raw/main/patches/stable/linux-6.13-bore/$_bore
  https://github.com/sirlucjan/kernel-patches/raw/master/6.13/kbuild-cachyos-patches/0001-Cachy-Allow-O3.patch
  0001-ZEN-Add-sysctl-and-CONFIG-to-disallow-unprivileged.patch
  0002-arch-Kconfig-Default-to-maximum-amount-of-ASLR-bits.patch
  0003-drivers-firmware-skip-simpledrm-if-nvidia-drm-modeset-1-is.patch
  0004-fuse-prevent-folio-use-after-free-in-readahead.patch
  0005-mei-vsc-Use-wakeuphostint-when-getting-the-host-wakeup.patch
)
validpgpkeys=(
  ABAF11C65A2970B130ABE3C479BE3E4300411886  # Linus Torvalds
  647F28654894E3BD457199BE38DBBDC86092693E  # Greg Kroah-Hartman
)
sha256sums=('b80e0bc8efbc31e9ce5a84d1084dcccfa40e01bea8cc25afd06648b93d61339e'
            'SKIP'
            # config
            'aec7d06e4ffd3b30d1c926cc3e468a76228100e4753bf954937b6e0d009651c4'
            # gcc patch
            'b3fd8b1c5bbd39a577afcccf6f1119fdf83f6d72119f4c0811801bdd51d1bc61'
            # hrtimers patch
            #'afa9bf94d6820c86041c7d55c25b04fe7f1aec86adbe45cb282d285901e827b3'
            # bore patch
            '9154233475c2abdfa000d1cec1a5229fc8b6d1cfd5a70f542ebda01c9de0d73e'
            # -O3
            '3f77927776987f5569d71426cb3b2f800c8fe777fd1737a7e6461ea6aeb6e8f4'
            # archlinux patches
            'f9ffdfd5a2523d5a63dd2ac4523a5a7cfab8c64fb963ba5c41e1d62591d854aa'
            'c88f1f2e920f8e88b529839944f72bd50bb4466ef6642efc3310609299360138'
            'bdd6c833582c2fafa56d9c0edc58833d45ad44c998b72c3502c0e51856b65d01'
            'b7b1a4d3940c0e54cf8cdb5b7637345273866df9b63fc485530fa8f48791004b'
            '0db0bd36cf8e162fe2d66a4e99b0bdb29040fc6b0e3e479590c134a23d1a4356'
)

prepare() {
  cd linux-${pkgver}

  msg2 "Setting version..."
  echo "-$pkgrel" > localversion.10-pkgrel
  echo "${pkgbase#linux}" > localversion.20-pkgname

  local src
  for src in "${source[@]}"; do
    src="${src%%::*}"
    src="${src##*/}"
    src="${src%.zst}"
    [[ $src = 0*.patch ]] || continue
    echo "Applying patch $src..."
    patch -Np1 < "../$src"
  done

  echo "Setting config..."
  cp ../config .config

  # https://bbs.archlinux.org/viewtopic.php?pid=1824594#p1824594
  scripts/config --enable CONFIG_PSI_DEFAULT_DISABLED

  # https://bbs.archlinux.org/viewtopic.php?pid=1863567#p1863567
  scripts/config --disable CONFIG_LATENCYTOP
  scripts/config --disable CONFIG_SCHED_DEBUG

  # FS#66613
  # https://bugzilla.kernel.org/show_bug.cgi?id=207173#c6
  scripts/config --disable CONFIG_KVM_WERROR

  # ck recommends 1000 Hz tick and the hrtimer patches in lieu of ck1
  scripts/config --enable CONFIG_HZ_1000

  # these are ck's htrimer patches
  #echo "Patching with ck hrtimer patches..."

  #for i in ../linux-patches-"$_commit"/"$_ckhrtimer"/ck-hrtimer/0*.patch; do
  #  patch -Np1 -i $i
  #done

  if [[ -n "$_clangbuild" ]]; then
    scripts/config -e LTO_CLANG_THIN
    export _LLVM=1
    export _LLVM_IAS=$_LLVM
  fi

  diff -u ../config .config || :

  # https://github.com/graysky2/kernel_gcc_patch
  # make sure to apply after olddefconfig to allow the next section
  msg2 "Patching to enable GCC optimization for other uarchs..."
  patch -Np1 -i "$srcdir/kernel_compiler_patch-$_gcc_more_v/more-ISA-levels-and-uarches-for-kernel-6.1.79+.patch"

  # since there are multiple options in the above patch (uarch + ISA setting), the yes method that worked
  # in the past will no long work so remove it
  make LLVM=$_LLVM LLVM_IAS=$_LLVM olddefconfig

  ### Optionally load needed modules for the make localmodconfig
  # See https://aur.archlinux.org/packages/modprobed-db
    if [ -n "$_localmodcfg" ]; then
      if [ -f $HOME/.config/modprobed.db ]; then
        echo "Running Steven Rostedt's make localmodconfig now"
        make LLVM=$_LLVM LLVM_IAS=$_LLVM LSMOD="$HOME/.config/modprobed.db" localmodconfig
      else
        echo "No modprobed.db data found"
        exit
      fi
    fi

  make -s kernelrelease > version
  msg2 "Prepared $pkgbase version $(<version)"

  [[ -z "$_makenconfig" ]] || make LLVM=$_LLVM LLVM_IAS=$_LLVM nconfig

  # save configuration for later reuse
  cat .config > "${startdir}/config.last"

  # uncomment if you want to build with distcc
  ### sed -i '/HAVE_GCC_PLUGINS/d' arch/x86/Kconfig
}

build() {
  cd linux-${pkgver}
  make LLVM=$_LLVM LLVM_IAS=$_LLVM all
  make LLVM=$_LLVM LLVM_IAS=$_LLVM -C tools/bpf/bpftool vmlinux.h feature-clang-bpf-co-re=1
}

_package() {
  pkgdesc="The Linux kernel and modules with ck's hrtimer patches"
  depends=(
    coreutils
    initramfs
    kmod
  )
  optdepends=(
    'wireless-regdb: to set the correct wireless channels of your country'
    'linux-firmware: firmware images needed for some devices'
  )
  provides=(
    KSMBD-MODULE
    VIRTUALBOX-GUEST-MODULES
    WIREGUARD-MODULE
  )
  replaces=(
    virtualbox-guest-modules-arch
    wireguard-arch
  )
 
  cd linux-${pkgver}
  local modulesdir="$pkgdir/usr/lib/modules/$(<version)"

  echo "Installing boot image..."
  # systemd expects to find the kernel here to allow hibernation
  # https://github.com/systemd/systemd/commit/edda44605f06a41fb86b7ab8128dcf99161d2344
  #install -Dm644 "$(make -s image_name)" "$modulesdir/vmlinuz"
  #
  # hard-coded path in case user defined CC=xxx for build which causes errors
  # see this FS https://bugs.archlinux.org/task/64315
  install -Dm644 arch/x86/boot/bzImage "$modulesdir/vmlinuz"

  # Used by mkinitcpio to name the kernel
  echo "$pkgbase" | install -Dm644 /dev/stdin "$modulesdir/pkgbase"

  echo "Installing modules..."
  ZSTD_CLEVEL=19 make LLVM=$_LLVM LLVM_IAS=$_LLVM INSTALL_MOD_PATH="$pkgdir/usr" INSTALL_MOD_STRIP=1 \
    DEPMOD=/doesnt/exist modules_install  # Suppress depmod

  # remove build link
  rm "$modulesdir"/build
}

_package-headers() {
  pkgdesc="Headers and scripts for building modules for ${pkgbase/linux/Linux} kernel"
  depends=(pahole "$pkgbase") # added to keep kernel and headers packages matched
  
  cd linux-${pkgver}
  local builddir="$pkgdir/usr/lib/modules/$(<version)/build"

  echo "Installing build files..."
  install -Dt "$builddir" -m644 .config Makefile Module.symvers System.map \
    localversion.* version vmlinux tools/bpf/bpftool/vmlinux.h
  install -Dt "$builddir/kernel" -m644 kernel/Makefile
  install -Dt "$builddir/arch/x86" -m644 arch/x86/Makefile
  cp -t "$builddir" -a scripts

  # required when STACK_VALIDATION is enabled
  install -Dt "$builddir/tools/objtool" tools/objtool/objtool

  # required when DEBUG_INFO_BTF_MODULES is enabled
  install -Dt "$builddir/tools/bpf/resolve_btfids" tools/bpf/resolve_btfids/resolve_btfids

  echo "Installing headers..."
  cp -t "$builddir" -a include
  cp -t "$builddir/arch/x86" -a arch/x86/include
  install -Dt "$builddir/arch/x86/kernel" -m644 arch/x86/kernel/asm-offsets.s

  install -Dt "$builddir/drivers/md" -m644 drivers/md/*.h
  install -Dt "$builddir/net/mac80211" -m644 net/mac80211/*.h

  # https://bugs.archlinux.org/task/13146
  install -Dt "$builddir/drivers/media/i2c" -m644 drivers/media/i2c/msp3400-driver.h

  # https://bugs.archlinux.org/task/20402
  install -Dt "$builddir/drivers/media/usb/dvb-usb" -m644 drivers/media/usb/dvb-usb/*.h
  install -Dt "$builddir/drivers/media/dvb-frontends" -m644 drivers/media/dvb-frontends/*.h
  install -Dt "$builddir/drivers/media/tuners" -m644 drivers/media/tuners/*.h

  # https://bugs.archlinux.org/task/71392
  install -Dt "$builddir/drivers/iio/common/hid-sensors" -m644 drivers/iio/common/hid-sensors/*.h

  echo "Installing KConfig files..."
  find . -name 'Kconfig*' -exec install -Dm644 {} "$builddir/{}" \;

  echo "Removing unneeded architectures..."
  local arch
  for arch in "$builddir"/arch/*/; do
    [[ $arch = */x86/ ]] && continue
    echo "Removing $(basename "$arch")"
    rm -r "$arch"
  done

  echo "Removing documentation..."
  rm -r "$builddir/Documentation"

  echo "Removing broken symlinks..."
  find -L "$builddir" -type l -printf 'Removing %P\n' -delete

  echo "Removing loose objects..."
  find "$builddir" -type f -name '*.o' -printf 'Removing %P\n' -delete

  echo "Stripping build tools..."
  local file
  while read -rd '' file; do
    case "$(file -Sib "$file")" in
      application/x-sharedlib\;*)      # Libraries (.so)
        strip -v $STRIP_SHARED "$file" ;;
      application/x-archive\;*)        # Libraries (.a)
        strip -v $STRIP_STATIC "$file" ;;
      application/x-executable\;*)     # Binaries
        strip -v $STRIP_BINARIES "$file" ;;
      application/x-pie-executable\;*) # Relocatable binaries
        strip -v $STRIP_SHARED "$file" ;;
    esac
  done < <(find "$builddir" -type f -perm -u+x ! -name vmlinux -print0)

  echo "Stripping vmlinux..."
  strip -v $STRIP_STATIC "$builddir/vmlinux"

  echo "Adding symlink..."
  mkdir -p "$pkgdir/usr/src"
  ln -sr "$builddir" "$pkgdir/usr/src/$pkgbase"
}

pkgname=("$pkgbase" "$pkgbase-headers")
for _p in "${pkgname[@]}"; do
  eval "package_$_p() {
    $(declare -f "_package${_p#$pkgbase}")
    _package${_p#$pkgbase}
  }"
done

# vim:set ts=8 sts=2 sw=2 et:
