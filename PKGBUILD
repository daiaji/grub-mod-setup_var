# Based on the file created for Arch Linux by:
# Contributor: Christian Hesse <mail@eworm.de>
# Contributor: Llewelyn Trahaearn <WoefulDerelict [at] GMail [dot] com>
# Contributor: Tobias Powalowski <tpowa [at] archlinux [dot] org>
# Contributor: Ronald van Haren <ronald [at] archlinux [dot] org>
# Contributor: Keshav Amburay <(the ddoott ridikulus ddoott rat) (aatt) (gemmaeiil) (ddoott) (ccoomm)>

# Maintainer: Philip Mueller <philm|manjaro|org>
# Maintainer: Guinux <nuxgui|gmail|com>

## "1" to enable IA32-EFI build in Arch x86_64, "0" to disable
_ia32_efi_in_arch_x64="1"

## "1" to enable EMU build, "0" to disable
_grub_emu_build="1"

_GRUB_INT_VER="2.05"
_GRUB_COMMIT="d7cac52eb21b866ffec66dc8d1319cc0695c94ee"
_GRUB_EXTRAS_COMMIT="8a245d5c1800627af4cefa99162a89c7a46d8842"
_GNULIB_COMMIT="387d654cabd7bc1594b1fc8d195cd9b66f820296"

[[ "${CARCH}" == "x86_64" ]] && _target_arch="x86_64"
[[ "${CARCH}" == "i686" ]] && _target_arch="i386"

_build_platforms="i386-pc ${_target_arch}-efi"
[[ "${CARCH}" == "x86_64" ]] && [[ "${_ia32_efi_in_arch_x64}" == "1" ]] && _build_platforms+=" i386-efi"
[[ "${_grub_emu_build}" == "1" ]] && _build_platforms+=" ${_target_arch}-emu"

pkgname="grub"
pkgver=2.04
pkgrel=12
pkgdesc="GNU GRand Unified Bootloader (2)"
arch=('x86_64' 'i686')
url="https://www.gnu.org/software/grub/"
license=('GPL3')
depends=('device-mapper' 'freetype2' 'fuse2' 'gettext')
makedepends=('autogen' 'bdf-unifont' 'git' 'help2man'
             'python' 'rsync' 'texinfo' 'ttf-dejavu')
optdepends=('dosfstools: For grub-mkrescue FAT FS and EFI support'
            'efibootmgr: For grub-install EFI support'
            'libisoburn: Provides xorriso for generating grub rescue iso using grub-mkrescue'
            'mtools: For grub-mkrescue FAT FS support'
            'os-prober: To detect other OSes when generating grub.cfg in BIOS systems')

if [[ "${_grub_emu_build}" == "1" ]]; then
    depends+=('sdl')
    makedepends+=('libusb')
    optdepends+=('libusb: For grub-emu USB support')
fi

provides=("${pkgname%-*}")
conflicts=("${pkgname%-*}")
backup=('etc/default/grub'
        'etc/grub.d/40_custom'
        'boot/grub/grub.cfg')
install="${pkgname}.install"
source=("grub::git+https://git.savannah.gnu.org/git/grub.git#commit=$_GRUB_COMMIT"
        "grub-extras::git+https://git.savannah.gnu.org/git/grub-extras.git#commit=$_GRUB_EXTRAS_COMMIT"
        "gnulib::git+https://git.savannah.gnu.org/git/gnulib.git#commit=$_GNULIB_COMMIT"
        'grub-export-path.patch'
        'grub-add-GRUB_COLOR_variables.patch'
        'grub-manjaro-modifications.patch'
        'grub-use-efivarfs.patch'
        '0001-grub-maybe_quiet.patch'
        '0002-grub-gettext_quiet.patch'
        '0003-grub-quick-boot.patch'
        'setup_var-patch.patch'
        'background.png'
        'grub.default'
        'grub.cfg'
        'update-grub'
        'grub-set-bootflag'
        "${pkgname}.hook")
sha256sums=('SKIP'
            'SKIP'
            'SKIP'
            '63c611189a60d68c6ae094f2ced91ac576b3921b7fd2e75a551c2dc6baefc35e'
            'a5198267ceb04dceb6d2ea7800281a42b3f91fd02da55d2cc9ea20d47273ca29'
            'cf00c96aee37e0a73c1ab6ed6ccfe74fa2b2859f55cd315a4caa6c880ce7aeba'
            '20b2b6e7f501596b5cce6ffa05906980427f760c03d308d0e045cf2ecf47bb0e'
            '9a0ef2efe572f3e206d8f145cb9a00098f44d41eaf396110810f6f79885bd5de'
            '39d7843dfe1e10ead912a81be370813b8621794a7967b3cc5e4d4188b5bf7264'
            'd222ea6e676268f44abdc2c92e9e4a6265646525ace108b828f4eb01cf20f8dd'
            '4531ab7789650f20415b0b88e15414ea5ebb71f365a28fd376cfa2c08b056722'
            '01264c247283b7bbdef65d7646541c022440ddaf54f8eaf5aeb3a02eb98b4dd8'
            'f1b226fe671665305e21e06dc1744b94f734e397697e2e9915bd95fbcd0c6ef5'
            '7fc95d49c0febe98a76e56b606a280565cb736580adecf163bc6b5aca8e7cbd8'
            '467b0101154076fee99d9574a5fb6b772a3923cc200a1f4ca08fe17be8d68111'
            '2eb199f510340cf8d190ba2fa80d5bdcf1e2e7ca53e8011af2ee62ea3b8dd03b'
            'a97ddf6694fa5070463a2d3f997205436a63fbe125071dd1bef0d59999adff70')
 
prepare() {
    cd grub

    # https://github.com/calamares/calamares/issues/918
    echo "Use efivarfs modules"
    patch -Np1 -i "${srcdir}/grub-use-efivarfs.patch"
    echo

    echo "Patch to export $PATH"
    patch -Np1 -i "${srcdir}/grub-export-path.patch"
    echo
    
    echo "Patch to enable GRUB_COLOR_* variables in grub-mkconfig"
    ## Based on http://lists.gnu.org/archive/html/grub-devel/2012-02/msg00021.html
    patch -Np1 -i "${srcdir}/grub-add-GRUB_COLOR_variables.patch"
    echo

    echo "Patch to include Manjaro Linux Modifications"
    patch -Np1 -i "${srcdir}/grub-manjaro-modifications.patch"
    echo

    echo "Add Ubuntu patches"
    patch -Np1 -i "${srcdir}/0001-grub-maybe_quiet.patch"
    patch -Np1 -i "${srcdir}/0002-grub-gettext_quiet.patch"
    patch -Np1 -i "${srcdir}/0003-grub-quick-boot.patch"
    
	echo "Patch Setup_var..."
	patch -Np1 -i "${srcdir}/setup_var-patch.patch"

    echo "Fix DejaVuSans.ttf location so that grub-mkfont can create *.pf2 files for starfield theme..."
    sed 's|/usr/share/fonts/dejavu|/usr/share/fonts/dejavu /usr/share/fonts/TTF|g' -i "configure.ac"

    echo "Fix mkinitcpio 'rw' FS#36275..."
    sed 's| ro | rw |g' -i "util/grub.d/10_linux.in"

    echo "Fix OS naming FS#33393..."
    sed 's|GNU/Linux|Linux|' -i "util/grub.d/10_linux.in"

    echo "Bump Version to ${pkgver}~manjaro"
    sed -i -e "s|${_GRUB_INT_VER}|${pkgver}~manjaro|g" "configure.ac"

    echo "Pull in latest language files"
    ./linguas.sh

    # Remove lua module from grub-extras as it is incompatible with changes to grub_file_open   
    # http://git.savannah.gnu.org/cgit/grub.git/commit/?id=ca0a4f689a02c2c5a5e385f874aaaa38e151564e
    rm -rf "$srcdir"/grub-extras/lua
}

build() {
    cd grub
    export GRUB_CONTRIB="$srcdir"/grub-extras
    export GNULIB_SRCDIR="$srcdir"/gnulib

    # Undefined references to __stack_chk_fail
    CFLAGS=${CFLAGS/-fstack-protector-strong}

    # Undefined references to _GLOBAL_OFFSET_TABLE_
    CFLAGS=${CFLAGS/-fno-plt}

    ./bootstrap

    for _arch in $_build_platforms; do
        mkdir "$srcdir"/grub/build_"$_arch"
        cd "$srcdir"/grub/build_"$_arch"

        # Explicitly set ac_cv_header_sys_sysmacros_h
        # https://savannah.gnu.org/bugs/index.php?55520

        # * _FORTIFY_SOURCE requires compiling with optimization warnings
        #   become errors due to a -Werror added during ./configure tests.
        #   This results in an incorrect configuration and only by adding -O2
        #   to CPPFLAGS does this problem seem to be worked around.
        ../configure --with-platform="${_arch##*-}" \
                --target="${_arch%%-*}"  \
                --prefix="/usr" \
                --sbindir="/usr/bin" \
                --sysconfdir="/etc" \
                --enable-boot-time \
                --enable-cache-stats \
                --enable-device-mapper \
                --enable-grub-mkfont \
                --enable-grub-mount \
                --enable-mm-debug \
                --enable-nls \
                --disable-silent-rules \
                --disable-werror \
                --with-bootdir="/boot" \
                --with-grubdir="grub" \
                --enable-quiet-boot \
                --enable-quick-boot \
                CPPFLAGS="$CPPFLAGS -O2" \
                ac_cv_header_sys_sysmacros_h=yes
        make
    done
}

package() {
    cd grub

    for _arch in $_build_platforms; do
        cd "$srcdir"/grub/build_"$_arch"
        make DESTDIR="$pkgdir" bashcompletiondir=/usr/share/bash-completion/completions install
    done

    msg "Install grub.cfg for backup array"
    install -D -m0644 "${srcdir}/grub.cfg" "${pkgdir}/boot/grub/grub.cfg"

    msg "Install update-grub"
    install -Dm755 "${srcdir}/update-grub" "${pkgdir}/usr/bin/update-grub"

    msg "Install grub background"
    install -Dm644 "${srcdir}/background.png" "${pkgdir}/usr/share/grub/background.png"	

    # Install /etc/default/grub (used by grub-mkconfig)
    install -D -m0644 "$srcdir"/grub.default "$pkgdir"/etc/default/grub

    install -D -m644 "${srcdir}/${pkgname}.hook" "${pkgdir}/usr/share/libalpm/hooks/99-${pkgname}.hook"

    # workaround for quiet fsck
    install -D -m755 "${srcdir}/grub-set-bootflag" "${pkgdir}/usr/bin/grub-set-bootflag"
    
    # Tidy up
    find "$pkgdir"/usr/lib/grub \( -name '*.module' -o \
                                   -name '*.image' -o \
                                   -name 'kernel.exec' -o \
                                   -name 'gdb_grub' -o \
                                   -name 'gmodule.pl' \) -delete
}
