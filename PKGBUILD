# Maintainer: Vitaly Ankh <https://aur.archlinux.org/account/VitalyAnkh>
# Maintainer of emacs-git: Pedro A. López-Valencia <https://aur.archlinux.org/users/vorbote>
# Maintainer of emacs-pgtk-native-comp: Andrew Whatson <https://aur.archlinux.org/account/flatwhatson>

################################################################################
# The difference between this PKGBUILD and the one from `emacs-git` is that:
# - this one builds emacs from flatwhatson's `pgtk-nativecomp` branch, which
#   contains an up-to-date merge of masm11 and fejfighter's pgtk work with
#   the feature/native-comp branch from the official emacs repo.
# - the pure-GTK3 rendering backend is enabled.
# - the xwidgets webkit2gtk is disabled for xwidgets doesn't support pgtk.
# - link-time optimization is enabled by default.
# - enalbe JIT and AOT compilation of emacs-lisp, which
#   means built-in packages and your own packages are 
#   native compiled by default.
################################################################################

################################################################################
# CAVEAT LECTOR: This PKGBUILD is highly opinionated. I give you
#                enough rope to hang yourself, but by default it
#                only enables the features I use.
#
#        TLDR: yaourt users, cry me a river.
#
#        Everyone else: do not update blindly this PKGBUILD. At least
#        make sure to compare and understand the changes.
#
################################################################################

################################################################################
# Assign "YES" to the variable you want enabled; empty or any other value
# for NO.
#
# Where you read experimental, replace with foobar.
# =================================================

################################################################################
USE_ALL_CPU_CORES="YES" # Do you want to use all CPU cores?

CHECK=            # Run tests. May fail, this is developement after all.

CLANG="YES"       # Use clang.

GOLD=             # Use the gold linker.

LTO="YES"         # Enable link-time optimization. Still experimental.
                  # This compiles only performance critical elisp files.
                  #
                  # To compile all elisp on demand, add
                  #    (setq comp-deferred-compilation t)
                  # to your .emacs file.

MOLD="MOLD"       # Use the mold linker.

AOT="YES"         # Precompile all included elisp. It takes a long time.
                  # You still need to enable on-demand compilation
                  # for your own packages.

CLI=              # CLI only binary.

GPM="YES"         # Mouse support in Linux console using gmpd.

NOTKIT=           # Use no toolkit widgets. Like B&W Twm (001d sk00l).
                  # Bitmap fonts only, 1337!
               
LUCID=            # Use the lucid, a.k.a athena, toolkit. Like XEmacs, sorta.
                  #
                  # Read https://wiki.archlinux.org/index.php/X_resources
                  # https://en.wikipedia.org/wiki/X_resources
                  # and https://www.emacswiki.org/emacs/XftGnuEmacs
                  # for some tips on using outline fonts with
                  # Xft, if you choose no toolkit or Lucid.
                  #
               
ALSA="YES"             # Linux sound support.

NOCAIRO=          # Disable here. 
               
XWIDGETS=         # Use GTK+ widgets pulled from webkit2gtk. Usable.
               
DOCS_HTML=        # Generate and install html documentation.
               
DOCS_PDF=         # Generate and install pdf documentation.
               
NOGZ="YES"        # Don't compress .el files.

JIT="YES"         # Enable JIT compilation.
################################################################################

################################################################################
if [[ $CLI == "YES" ]] ; then
  pkgname="emacs-nox-git"
else
pkgname="emacs-native-comp-git-enhanced"
fi
pkgver=29.0.50.152957
pkgrel=1
pkgdesc="GNU Emacs. Development master branch."
arch=('x86_64')
url="http://www.gnu.org/software/emacs/"
license=('GPL3')
depends_nox=('gnutls' 'libxml2' 'jansson')
depends=("${depends_nox[@]}" 'harfbuzz')
makedepends=('git')
provides=('emacs' 'emacs26-git' 'emacs-27-git' 'emacs28-git' 'emacs-seq' 'emacs-nox')
conflicts=('emacs' 'emacs26-git' 'emacs-27-git' 'emacs28-git' 'emacs-seq' 'emacs-nox')
replaces=('emacs' 'emacs26-git' 'emacs-27-git' 'emacs28-git' 'emacs-seq' 'emacs-nox')
source=("emacs-git::git://git.savannah.gnu.org/emacs.git#branch=feature/pgtk")
# If Savannah fails for reasons, use Github's mirror
#source=("emacs-git::git://github.com/emacs-mirror/emacs.git")
options=(!strip)
install=emacs-git.install
b2sums=('SKIP')
################################################################################

################################################################################

if [[ $GOLD == "YES" && ! $CLANG == "YES" ]]; then
  export LD=/usr/bin/ld.gold
  export CFLAGS+=" -fuse-ld=gold";
  export CXXFLAGS+=" -fuse-ld=gold";
elif [[ $GOLD == "YES" && $CLANG == "YES" ]]; then
  echo "";
  echo "Clang rather uses its own linker.";
  echo "";
  exit 1;
fi

if [[ $MOLD == "YES" && ! $CLANG == "YES" ]]; then
  echo "";
  echo "I don't recommend use mold with gcc...";
  echo "";
  exit 1;
elif [[ $MOLD == "YES" && $CLANG == "YES" ]]; then
  export LD=/usr/bin/mold
  export CFLAGS+=" --ld-path=/usr/bin/mold";
  export CXXFLAGS+=" --ld-path=/usr/bin/mold";
fi

if [[ $CLANG == "YES" ]]; then
  export CC="/usr/bin/clang" ;
  export CXX="/usr/bin/clang++" ;
  export CPP="/usr/bin/clang -E" ;
  export LD="/usr/bin/lld" ;
  export AR="/usr/bin/llvm-ar" ;
  export AS="/usr/bin/llvm-as" ;
if [[ $MOLD == "YES" ]]; then
  export LD=/usr/bin/mold
  export CFLAGS+=" --ld-path=/usr/bin/mold";
  export CXXFLAGS+=" --ld-path=/usr/bin/mold";
  makedepends+=( 'clang' 'mold' 'llvm') ;
else
  export CCFLAGS+=' -fuse-ld=lld' ;
  export CXXFLAGS+=' -fuse-ld=lld' ;
  makedepends+=( 'clang' 'lld' 'llvm') ;
fi
fi

if [[ $JIT == "YES" ]]; then
  if [[ $CLI == "YES" ]]; then
    depends_nox+=( 'libgccjit' );
  else
    depends+=( 'libgccjit' );
  fi
fi

if [[ $CLI == "YES" ]]; then
  depends=("${depends_nox[@]}");
elif [[ $NOTKIT == "YES" ]]; then
  depends+=( 'dbus' 'hicolor-icon-theme' 'libxinerama' 'libxrandr' 'lcms2' 'librsvg' 'libxfixes' );
  makedepends+=( 'xorgproto' );
elif [[ $LUCID == "YES" ]]; then
  depends+=( 'dbus' 'hicolor-icon-theme' 'libxinerama' 'libxfixes' 'lcms2' 'librsvg' 'xaw3d' 'libxrandr' );
  makedepends+=( 'xorgproto' );
else
  depends+=( 'gtk3' );
  makedepends+=( 'xorgproto' );
fi

if [[ ! $NOX == "YES" ]] && [[ ! $CLI == "YES" ]]; then
  depends+=( 'libjpeg-turbo' 'giflib' );
elif [[ $CLI == "YES" ]]; then
  depends+=();
fi

if [[ $ALSA == "YES" ]]; then
  if [[ $CLI == "YES" ]]; then
    depends_nox+=( 'alsa-lib' );
  else
    depends+=( 'alsa-lib' );
  fi
fi

if [[ ! $NOCAIRO == "YES" ]] && [[ ! $CLI == "YES" ]] ; then
  depends+=( 'cairo' );
fi

if [[ $XWIDGETS == "YES" ]]; then
  if [[ $LUCID == "YES" ]] || [[ $NOTKIT == "YES" ]] || [[ $CLI == "YES" ]]; then
    echo "";
    echo "";
    echo "Xwidgets support **requires** GTK+3!!!";
    echo "";
    echo "";
    exit 1;
  else
    depends+=( 'webkit2gtk' );
  fi
fi

if [[ $GPM == "YES" ]]; then
  if [[ $CLI == "YES" ]]; then
    depends_nox+=( 'gpm' );
  else
    depends+=( 'gpm' );
  fi
fi

if [[ $DOCS_PDF == "YES" ]]; then
  makedepends+=( 'texlive-core' );
fi
################################################################################

################################################################################
pkgver() {
  cd "$srcdir/emacs-git"

  printf "%s.%s" \
    "$(grep AC_INIT configure.ac | \
    sed -e 's/^.\+\ \([0-9]\+\.[0-9]\+\.[0-9]\+\?\).\+$/\1/')" \
    "$(git rev-list --count HEAD)"
}

# There is no need to run autogen.sh after first checkout.
# Doing so, breaks incremental compilation.
prepare() {
  cd "$srcdir/emacs-git"
  [[ -x configure ]] || ( ./autogen.sh git && ./autogen.sh autoconf )
}

if [[ $CHECK == "YES" ]]; then
check() {
  cd "$srcdir/emacs-git"
  make check
}
fi

build() {
  cd "$srcdir/emacs-git"

  local _conf=(
    --prefix=/usr
    --sysconfdir=/etc
    --libexecdir=/usr/lib
    --localstatedir=/var
    --mandir=/usr/share/man
    --with-gameuser=:games
    --with-sound=alsa
    --with-modules
    --without-libotf
    --without-m17n-flt
# Beware https://debbugs.gnu.org/cgi/bugreport.cgi?bug=25228
# dconf and gconf break font settings you set in ~/.emacs.
# If you insist you'll need to read that bug report in *full*.
# Good luck!
   --without-gconf
   --without-gsettings
# Pure gtk, which will enable wayland support.
   --with-pgtk
  )

################################################################################

################################################################################

if [[ $CLANG == "YES" ]]; then
  _conf+=( '--enable-autodepend' );
fi

if [[ $LTO == "YES" ]]; then
  _conf+=( '--enable-link-time-optimization' );
fi

if [[ $JIT == "YES" ]]; then
  _conf+=( '--with-native-compilation' );
fi

if [[ $CLI == "YES" ]]; then
  _conf+=( '--without-x' '--with-x-toolkit=no' '--without-xft' '--without-lcms2' '--without-rsvg' '--without-jpeg' '--without-gif' '--without-tiff' '--without-png' );
elif [[ $NOTKIT == "YES" ]]; then
  _conf+=( '--with-x-toolkit=no' '--without-toolkit-scroll-bars' '--without-xft' '--without-xaw3d' );
elif [[ $LUCID == "YES" ]]; then
  _conf+=( '--with-x-toolkit=lucid' '--with-xft' '--with-xaw3d' );
else
  _conf+=( '--with-x-toolkit=gtk3' '--without-xaw3d' );
fi

if [[ $NOCAIRO == "YES" || $CLI == "YES" ]]; then
  _conf+=( '--without-cairo' );
fi

if [[ $ALSA == "YES" ]]; then
    _conf+=( '--with-sound=alsa' );
else
    _conf+=( '--with-sound=no' );
fi

if [[ $XWIDGETS == "YES" ]]; then
  _conf+=( '--with-xwidgets' );
fi

if [[ $GPM == "YES" ]]; then
    true
else
  _conf+=( '--without-gpm' );
fi

if [[ $NOGZ == "YES" ]]; then
  _conf+=( '--without-compress-install' );
fi

# ctags/etags may be provided by other packages, e.g, universal-ctags
_conf+=('--program-transform-name=s/\([ec]tags\)/\1.emacs/')

################################################################################

################################################################################

  ./configure "${_conf[@]}"

  # Using "make" instead of "make bootstrap" enables incremental
  # compiling. Less time recompiling. Yay! But you may
  # need to use bootstrap sometimes to unbreak the build.
  # Just add it to the command line.
  #
  # Please note that incremental compilation implies that you
  # are reusing your src directory!
  #
if [[ $USE_ALL_CPU_CORES == "YES" ]]; then
   if [[ $JIT == "YES" ]] && [[ $AOT == "YES" ]]; then
    make NATIVE_FULL_AOT=1 -j$(nproc)
  else
    make -j$(nproc)
  fi 
else
  if [[ $JIT == "YES" ]] && [[ $AOT == "YES" ]]; then
    make NATIVE_FULL_AOT=1
  else
    make
  fi
fi

  # You may need to run this if 'loaddefs.el' files become corrupt.
  #cd "$srcdir/emacs-git/lisp"
  #make autoloads
  #cd ../

  # Optional documentation formats.
  if [[ $DOCS_HTML == "YES" ]]; then
    make html;
  fi
  if [[ $DOCS_PDF == "YES" ]]; then
    make pdf;
  fi

}

package() {
  cd "$srcdir/emacs-git"

  make DESTDIR="$pkgdir/" install

  # Install optional documentation formats
  if [[ $DOCS_HTML == "YES" ]]; then make DESTDIR="$pkgdir/" install-html; fi
  if [[ $DOCS_PDF == "YES" ]]; then make DESTDIR="$pkgdir/" install-pdf; fi

  # fix user/root permissions on usr/share files
  find "$pkgdir"/usr/share/emacs/ | xargs chown root:root

  # fix permssions on /var/games
  mkdir -p "$pkgdir"/var/games/emacs
  chmod 775 "$pkgdir"/var/games
  chmod 775 "$pkgdir"/var/games/emacs
  chown -R root:games "$pkgdir"/var/games

}

################################################################################
# vim:set ft=bash ts=2 sw=2 et:
