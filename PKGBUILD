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
#  39. Intel Emerald Rapids (MEMERALDRAPIDS) (NEW)
#  40. Generic-x86-64 (GENERIC_CPU)
#  41. Generic-x86-64-v2 (GENERIC_CPU2) (NEW)
#  42. Generic-x86-64-v3 (GENERIC_CPU3) (NEW)
#  43. Generic-x86-64-v4 (GENERIC_CPU4) (NEW)
#  44. Intel-Native optimizations autodetected by the compiler (MNATIVE_INTEL) (NEW)
#  45. AMD-Native optimizations autodetected by the compiler (MNATIVE_AMD) (NEW)

### IMPORTANT: Do no edit below this line unless you know what you're doing
pkgbase=linux-xck
pkgver=6.9.7
pkgrel=2
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
_ckhrtimer=linux-6.8.y
_commit=ae3cbb29c43ca1baa6781f547d17b8ee5663e9d7

_gcc_more_v=20240221.2
_pstate=amd-pstate-patches-v12
_sched_ext=bore-sched-ext-patches-v24
source=(
  "https://www.kernel.org/pub/linux/kernel/v6.x/linux-$pkgver.tar".{xz,sign}
  config  # the main kernel config file
  "more-uarches-$_gcc_more_v.tar.gz::https://github.com/graysky2/kernel_compiler_patch/archive/$_gcc_more_v.tar.gz"
  "ck-hrtimer-$_commit.tar.gz::https://github.com/graysky2/linux-patches/archive/$_commit.tar.gz"
  #https://github.com/firelzrd/bore-scheduler/raw/main/patches/stable/linux-6.8-bore/$_bore
  #https://github.com/firelzrd/bore-scheduler/raw/main/patches/testing/linux-6.8-bore/$_bore
  https://github.com/sirlucjan/kernel-patches/raw/master/6.9/$_sched_ext/0001-bore-sched-ext-patches.patch
  https://github.com/sirlucjan/kernel-patches/raw/master/6.9/kbuild-cachyos-patches/0001-Cachy-Allow-O3.patch
  #https://github.com/sirlucjan/kernel-patches/raw/master/6.9/$_pstate/0001-amd-6.9-merge-changes-from-dev-tree.patch
  https://raw.githubusercontent.com/CachyOS/kernel-patches/master/6.9/0002-amd-pstate.patch
  0001-ZEN-Add-sysctl-and-CONFIG-to-disallow-unprivileged.patch
  0002-drivers-firmware-skip-simpledrm-if-nvidia-drm-modeset-1-is.patch
  0003-arch-Kconfig-Default-to-maximum-amount-of-ASLR-bits.patch
)
validpgpkeys=(
  ABAF11C65A2970B130ABE3C479BE3E4300411886  # Linus Torvalds
  647F28654894E3BD457199BE38DBBDC86092693E  # Greg Kroah-Hartman
)
sha256sums=('e4f588cd91eef9d461e5e14fdf9415feff8a72fbcc274089a0f768a58de001f8'
            'SKIP'
            # config
            'b0bfc34f8d5d9ccca7cda5f98fed7333272b2cf88de8d346f02128b43b3892a9'
            # gcc patch
            '1d3ac3e581cbc5108f882fcdc75d74f7f069654c71bad65febe5ba15a7a3a14f'
            # hrtimers patch
            '111adfc5b9c7d3bfd7d1a06286e7bee853dd1f51ecca3948eed39710eaf51381'
            # ext scheduler
            '0069ffb1fd32ef0808210c141c270e9b47e79b477aca1e5fd0e122e3232a1349'
            # bore testing
            #'ebf9b9f122f2cbaa549f2fc536524fc39c6308f862fab6e8fe0a75a6361d235d'
            # -O3
            '0f3def20686bec24c7db34019b5a22f5b74c6b439c8f7a39223d0f09fbc750bd'
            # AMD pstate patch
            'ee543c86a6e5ded5564f2f0deb5ea727802208e74baab75b88b193981e51b23b'
            # archlinux patches
            '6dce2ae80d898792a8314fcb1e329dcc14707dbaa04288cb1f569e96a0cf9fcb'
            '1bd2afd5086814ebaf3a3f2d7d84ce0dc9a72533a8f25338d403d79329c66e90'
            'd26231a7ad8976a90ad17cf413db503951d93641eb3d709c33c2fc1c27c2d197'
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
  msg2 "Patching to enable GCC optimization for other uarchs..."
  patch -Np1 -i "$srcdir/kernel_compiler_patch-$_gcc_more_v/more-uarches-for-kernel-6.8-rc4+.patch"

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
