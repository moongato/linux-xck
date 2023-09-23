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

# Optionally select a sub architecture by number or leave blank which will
# require user interaction during the build. Note that the generic (default)
# option is 39.
_subarch=

#  1. AMD Opteron/Athlon64/Hammer/K8 (MK8)
#  2. AMD Opteron/Athlon64/Hammer/K8 with SSE3 (MK8SSE3) (NEW)
#  3. AMD 61xx/7x50/PhenomX3/X4/II/K10 (MK10) (NEW)
#  4. AMD Barcelona (MBARCELONA) (NEW)
#  5. AMD Bobcat (MBOBCAT) (NEW)
#  6. AMD Jaguar (MJAGUAR) (NEW)
#  7. AMD Bulldozer (MBULLDOZER) (NEW)
#  8. AMD Piledriver (MPILEDRIVER) (NEW)
#  9. AMD Steamroller (MSTEAMROLLER) (NEW)
#  10. AMD Excavator (MEXCAVATOR) (NEW)
#  11. AMD Zen (MZEN) (NEW)
#  12. AMD Zen 2 (MZEN2) (NEW)
#  13. AMD Zen 3 (MZEN3) (NEW)
#  14. AMD Zen 4 (MZEN4) (NEW)
#  15. Intel P4 / older Netburst based Xeon (MPSC)
#  16. Intel Core 2 (MCORE2)
#  17. Intel Atom (MATOM)
#  18. Intel Nehalem (MNEHALEM) (NEW)
#  19. Intel Westmere (MWESTMERE) (NEW)
#  20. Intel Silvermont (MSILVERMONT) (NEW)
#  21. Intel Goldmont (MGOLDMONT) (NEW)
#  22. Intel Goldmont Plus (MGOLDMONTPLUS) (NEW)
#  23. Intel Sandy Bridge (MSANDYBRIDGE) (NEW)
#  24. Intel Ivy Bridge (MIVYBRIDGE) (NEW)
#  25. Intel Haswell (MHASWELL) (NEW)
#  26. Intel Broadwell (MBROADWELL) (NEW)
#  27. Intel Skylake (MSKYLAKE) (NEW)
#  28. Intel Skylake X (MSKYLAKEX) (NEW)
#  29. Intel Cannon Lake (MCANNONLAKE) (NEW)
#  30. Intel Ice Lake (MICELAKE) (NEW)
#  31. Intel Cascade Lake (MCASCADELAKE) (NEW)
#  32. Intel Cooper Lake (MCOOPERLAKE) (NEW)
#  33. Intel Tiger Lake (MTIGERLAKE) (NEW)
#  34. Intel Sapphire Rapids (MSAPPHIRERAPIDS) (NEW)
#  35. Intel Rocket Lake (MROCKETLAKE) (NEW)
#  36. Intel Alder Lake (MALDERLAKE) (NEW)
#  37. Intel Raptor Lake (MRAPTORLAKE) (NEW)
#  38. Intel Meteor Lake (MMETEORLAKE) (NEW)
#  39. Generic-x86-64 (GENERIC_CPU)
#  40. Generic-x86-64-v2 (GENERIC_CPU2) (NEW)
#  41. Generic-x86-64-v3 (GENERIC_CPU3) (NEW)
#  42. Generic-x86-64-v4 (GENERIC_CPU4) (NEW)
#  43. Intel-Native optimizations autodetected by the compiler (MNATIVE_INTEL) (NEW)
#  44. AMD-Native optimizations autodetected by the compiler (MNATIVE_AMD) (NEW)

### IMPORTANT: Do no edit below this line unless you know what you're doing
pkgbase=linux-xck
pkgver=6.5.5
pkgrel=1
arch=(x86_64)
license=(GPL2)
makedepends=(
  bc
  cpio
  gettext
  git
  libelf
  pahole
  python
  perl
  tar
  xz
)
[[ -n "$_clangbuild" ]] && makedepends+=(clang llvm lld python)
options=('!strip')

# https://ck-hack.blogspot.com/2021/08/514-and-future-of-muqss-and-ck-once.html
# acknowledgment to xanmod for initially keeping the hrtimer patches up to date
_ckhrtimer=linux-6.5.y
_commit=6ef0e9ff5db0ed380f98cc8c7436ca9c44ea32c9

_gcc_more_v=20221217
_bore=0001-linux6.5.y-bore3.1.4.patch
_xanmod=xanmod-patches
source=(
  "https://www.kernel.org/pub/linux/kernel/v6.x/linux-$pkgver.tar".{xz,sign}
  config  # the main kernel config file
  "more-uarches-$_gcc_more_v.tar.gz::https://github.com/graysky2/kernel_compiler_patch/archive/$_gcc_more_v.tar.gz"
  "ck-hrtimer-$_commit.tar.gz::https://github.com/graysky2/linux-patches/archive/$_commit.tar.gz"
  https://github.com/firelzrd/bore-scheduler/raw/main/patches/linux-6.5-bore/$_bore
  https://raw.githubusercontent.com/sirlucjan/kernel-patches/master/6.5/$_xanmod/0001-xanmod-patches.patch
  https://raw.githubusercontent.com/sirlucjan/kernel-patches/master/6.5/kbuild-cachyos-patches-sep/0001-Revert-kbuild-drop-support-for-CONFIG_CC_OPTIMIZE_FO.patch
  https://raw.githubusercontent.com/sirlucjan/kernel-patches/master/6.5/kbuild-cachyos-patches-sep/0002-init-Kconfig-enable-O3-for-all-arches.patch
  0001-ZEN-Add-sysctl-and-CONFIG-to-disallow-unprivileged.patch
  0002-drivers-firmware-skip-simpledrm-if-nvidia-drm.modeset-1-is-set.patch
  0003-ASoC-Intel-soc-acpi-fix-Dell-SKU-0B34.patch
  0004-btrfs-wait-on-uncached-block-groups-on-every-allocation-loop.patch
  0005-btrfs-set-last-dir-index-to-the-current-last-index-when.patch
  0006-btrfs-refresh-dir-last-index-during-a-rewinddir-3-call.patch
  0007-btrfs-fix-race-between-reading-a-directory-and-adding.patch
  0008-i915-Limit-the-length-of-an-sg-list-to-the-requested-length.patch
  0009-net-wwan-t7xx-Add-AP-CLDMA.patch
  0010-Revert-101bd907b424-misc-rtsx-judge-ASPM-Mode-to-set.patch
)
validpgpkeys=(
  ABAF11C65A2970B130ABE3C479BE3E4300411886  # Linus Torvalds
  647F28654894E3BD457199BE38DBBDC86092693E  # Greg Kroah-Hartman
)
sha256sums=('8cf10379f7df8ea731e09bff3d0827414e4b643dd41dc99d0af339669646ef95'
            'SKIP'
            # config
            '09b382ecf8e3dc10e7726eafbbf612f82599f3620035b8483d34a63c35b26540'
            # gcc patch
            'f1d586e111932890ad5e0df15d092fb9b3f87bae4ea17812aae9b0ec98fe2db0'
            # hrtimers patch
            'a86a59d089ddd4f31565ff6a27ba74f4697ffa0ab0f57dc839e1da6ab83d77a4'
            # bore scheduler
            '439b5ad573fa0f72589ca16deb3c41a5f09514a8dc60d19e10faa3d32338932f'
            # xanmod patch
            '5b1d973a2789b1a3439a5b4b8f063b2db11b23f8558796b8898c70faad666b08'
            # -O3
            '9d8de3547fb16e31f2b3ce8c9e7db9f535d01a419f7b4722d56ddeab4befec2d'
            'b2f8f84096b023519c3b64333cc0796283a0e88c3d5147a07423dbcc4cdfda0e'
            # archlinux patches
            '868778c7a022b4103842366adef15c60a048d32ad5515da003b72370e9b8d3a4'
            'ab1506d8b0e5e1eee6495579d036b33bb9e1d245f669b8a4f227c3cc94efb1be'
            '1825f66174511efcecece27de6ad296ff69200881a988a9ae8af9711c2e2569c'
            '327f8a1f94056da06d490fa7d4ddd5da0550cccb489bb41fdedb9e8653195906'
            '29d941d2826b970ea1439e306b6a8830c389147aaca53ea8cc13e3cb2a6e377f'
            'fdb827ffaf8106d3ca4bbbf78acebdc357f91f3f605eff6f8d2b3c94e2e52a75'
            '98801652f56844ba2897f59e74082a964db666db79727896a45a7a1f0f8a0ef5'
            'dfc26c437d5884045e7d8645bbd8f1dadb6820cc5ec01c019c4a454dae30f6cc'
            '4ad2a3b6fe5e87538ed0bfa50b570fc6ca0a81f1aa56517d6016117a3844755d'
            '012e88119af98d80e4701b578e3468b6c36cdcccef1c8c51bfd9de8680fea1be'        
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
  echo "Patching with ck hrtimer patches..."

  for i in ../linux-patches-"$_commit"/"$_ckhrtimer"/ck-hrtimer/0*.patch; do
    patch -Np1 -i $i
  done

  if [[ -n "$_clangbuild" ]]; then
    scripts/config -e LTO_CLANG_THIN
    export _LLVM=1
    export _LLVM_IAS=$_LLVM
  fi

  # non-interactively apply ck1 default options
  # this isn't redundant if we want a clean selection of subarch below
  make LLVM=$_LLVM LLVM_IAS=$_LLVM olddefconfig
  diff -u ../config .config || :

  # https://github.com/graysky2/kernel_gcc_patch
  # make sure to apply after olddefconfig to allow the next section
  echo "Patching to enable GCC optimization for other uarchs..."
  patch -Np1 -i "$srcdir/kernel_compiler_patch-$_gcc_more_v/more-uarches-for-kernel-5.17+.patch"

  if [ -n "$_subarch" ]; then
    # user wants a subarch so apply choice defined above interactively via 'yes'
    yes "$_subarch" | make LLVM=$_LLVM LLVM_IAS=$_LLVM oldconfig
  else
    # no subarch defined so allow user to pick one
    make LLVM=$_LLVM LLVM_IAS=$_LLVM oldconfig
  fi

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
  echo "Prepared $pkgbase version $(<version)"

  [[ -z "$_makenconfig" ]] || make LLVM=$_LLVM LLVM_IAS=$_LLVM nconfig

  # save configuration for later reuse
  cat .config > "${startdir}/config.last"

  # uncomment if you want to build with distcc
  ### sed -i '/HAVE_GCC_PLUGINS/d' arch/x86/Kconfig
}

build() {
  cd linux-${pkgver}
  make LLVM=$_LLVM LLVM_IAS=$_LLVM all
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

  # remove build and source links
  rm "$modulesdir"/{source,build}
}

_package-headers() {
  pkgdesc="Headers and scripts for building modules for ${pkgbase/linux/Linux} kernel"
  depends=(pahole "$pkgbase") # added to keep kernel and headers packages matched
  
  cd linux-${pkgver}
  local builddir="$pkgdir/usr/lib/modules/$(<version)/build"

  echo "Installing build files..."
  install -Dt "$builddir" -m644 .config Makefile Module.symvers System.map \
    localversion.* version vmlinux
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
