# $Id: PKGBUILD 114982 2014-07-05 02:54:37Z fyan $
# Maintainer : schuay <jakob.gruber@gmail.com>
# Contributor : kevin <kevin@archlinux.org>
# Contributor : Christian Schmidt <mucknert@gmx.net>
# Contributor : Markus Meissner <markus@meissna.de>
# Contributor : Nick Erdmann <erdmann@date.upb.de>

pkgname=nethack
pkgver=3.4.3
pkgrel=8
pkgdesc='A single player dungeon exploration game'
arch=('i686' 'x86_64')
url="http://www.nethack.org/index.html"
license=('custom')
depends=('ncurses' 'gzip')
groups=('from_abs')
install=nethack.install
source=("http://downloads.sourceforge.net/$pkgname/$pkgname-${pkgver//.}-src.tgz"
        'nh343-menucolor.diff'
        'nh343-statuscolors.patch'
        'nh343-acid-C343-179.diff'
        'nh343-astral-call-fix.v1.0.patch'
        'nh343-glyphcolor.diff'
        'nh343-beartraps.diff')
md5sums=('21479c95990eefe7650df582426457f9'
         'ade00f9cb51f1b0140557d329d56844c'
         'b11eeacbf6e58496563723dcf1047ac1'
         'a067ae1d0c1d0a72a181539c6f5e26f1'
         'ba388b863c90e6df708f9ab69c4a9bfc'
         'eb0be431054498c894a8fd7227f4f142'
         'a9b219d0c763c5cf92c9269f340e3493')

build(){
  cd $srcdir/$pkgname-$pkgver/
  sh sys/unix/setup.sh

  sed -e '/define HACKDIR/ s|/usr/games/lib/nethackdir|/var/games/nethack/|' \
      -e "/^#define COMPRESS\s/ s|/usr/bin/compress|$(which gzip)|" \
      -e '/^#define COMPRESS_EXTENSION/ s|".Z"|".gz"|' \
      -e 's|^/\* \(#define DLB\) \*/|\1|' -i include/config.h

  sed -e 's|^/\* \(#define LINUX\) \*/|\1|' \
      -e 's|^/\* \(#define TIMED_DELAY\) \*/|\1|' -i include/unixconf.h

  sed -e 's|^# \(WINTTYLIB = -lncurses\)|\1|' \
      -e 's|^WINTTYLIB = -ltermlib|#&|' -i src/Makefile

  # we are setting up for setgid games, so modify all necessary permissions
  # to allow full access for groups

  sed -e '/^GAMEDIR\s*=/ s|/games/.*$|/var/games/$(GAME)|' \
      -e '/^GAMEUID\s*=/ s|games|root|' \
      -e '/^GAMEGRP\s*=/ s|bin|games|' \
      -e '/^GAMEPERM\s*=/ s|04755|02755|' \
      -e '/^FILEPERM\s*=/ s|0644|0664|' \
      -e '/^DIRPERM\s*=/ s|0755|0775|' \
      -e '/^SHELLDIR\s*=/ s|/games|/usr/bin|' -i Makefile

  sed -e "/^MANDIR\s*=/s|/usr/man/man6|$pkgdir/usr/share/man/man6|" -i doc/Makefile

  # The order of applying the patches matters.
  patch -t -p1 <../nh343-menucolor.diff
  patch -t -p1 <../nh343-statuscolors.patch
  patch -t -p1 <../nh343-acid-C343-179.diff
  patch -t -p1 <../nh343-astral-call-fix.v1.0.patch
  patch -t -p1 <../nh343-glyphcolor.diff

  make
}
  
package() {
  cd $srcdir/$pkgname-$pkgver/

  install -dm755 $pkgdir/usr/share/{man/man6,doc/nethack}
  install -dm775 $pkgdir/var/games/
  make PREFIX=$pkgdir install manpages
  sed -e "s|HACKDIR=$pkgdir/|HACKDIR=/|" \
      -e 's|HACK=$HACKDIR|HACK=/usr/lib/nethack|' \
      -i $pkgdir/usr/bin/nethack

  install -dm755 $pkgdir/usr/lib/nethack
  mv $pkgdir/var/games/nethack/{nethack,recover} $pkgdir/usr/lib/nethack/

  install -Dm644 doc/Guidebook.txt $pkgdir/usr/share/doc/nethack/Guidebook.txt
      
  install -Dm644 dat/license $pkgdir/usr/share/licenses/$pkgname/LICENSE
}
