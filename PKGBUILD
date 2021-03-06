# Maintainer: Chocobo1 <chocobo1 AT archlinux DOT net>

pkgname=wpa_supplicant-git
pkgver=2.7.r13.g4c02242d0
pkgrel=1
pkgdesc="A daemon program controlling the wireless connection"
arch=('i686' 'x86_64' 'armv6h' 'armv7h' 'aarch64')
url="https://w1.fi/wpa_supplicant/"
license=('BSD')
depends=('glibc' 'openssl' 'libdbus' 'libnl' 'readline')
optdepends=('wpa_supplicant_gui: GUI frontend')
makedepends=('git')
provides=('wpa_supplicant')
conflicts=('wpa_supplicant')
source=("git://w1.fi/hostap.git"
        "config::https://git.archlinux.org/svntogit/packages.git/plain/trunk/config?h=packages/wpa_supplicant")
sha256sums=('SKIP'
            'SKIP')


prepare() {
  cd "$srcdir"

  echo "CONFIG_OWE=y" >> "$srcdir/config"
  echo "CONFIG_SAE=y" >> "$srcdir/config"
  echo "CONFIG_RRM=y" >> "$srcdir/config"
  echo "CONFIG_WNM=y" >> "$srcdir/config"
  echo "CONFIG_DRIVER_HOSTAP=y" >> "$srcdir/config"
  echo "CONFIG_DRIVER_NL80211=y" >> "$srcdir/config"
}

pkgver() {
  cd "hostap/wpa_supplicant"

  git describe --long --tags | sed 's/^hostap_//;s/\([^-]*-g\)/r\1/;s/[_-]/./g'
}

build() {
  cd "hostap/wpa_supplicant"

  cp "$srcdir/config" ".config"

  export CFLAGS="$CPPFLAGS $CFLAGS"
  make LIBDIR="/usr/lib" BINDIR="/usr/bin"
  make LIBDIR="/usr/lib" BINDIR="/usr/bin" eapol_test
}

package() {
  cd "hostap/wpa_supplicant"

  make LIBDIR="/usr/lib" BINDIR="/usr/bin" DESTDIR="$pkgdir" install

  install -Dm755 "eapol_test" "$pkgdir/usr/bin/eapol_test"

  install -d -m755 "$pkgdir/etc/wpa_supplicant"
  install -Dm644 "wpa_supplicant.conf" "$pkgdir/usr/share/doc/wpa_supplicant/wpa_supplicant.conf"

  install -d -m755 "$pkgdir/usr/share/dbus-1/system-services"
  install -m644 "dbus/fi.epitest.hostap.WPASupplicant.service" "$pkgdir/usr/share/dbus-1/system-services/"
  install -m644 "dbus/fi.w1.wpa_supplicant1.service" "$pkgdir/usr/share/dbus-1/system-services/"

  install -Dm644 "dbus/dbus-wpa_supplicant.conf" "$pkgdir/etc/dbus-1/system.d/wpa_supplicant.conf"

  install -d -m755 "$pkgdir/usr/lib/systemd/system"
  install -m644 systemd/*.service "$pkgdir/usr/lib/systemd/system/"

  sed -n '/This software may/,/OF SUCH DAMAGE./p' "README" > "COPYING"
  install -Dm644 "COPYING" "$pkgdir/usr/share/licenses/wpa_supplicant/COPYING"
}
