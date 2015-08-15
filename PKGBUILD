# $Id: PKGBUILD 72129 2012-06-08 23:50:59Z dreisner $
# Maintainer: Sergej Pupykin <pupykin.s+arch@gmail.com>

pkgname=bind-geodns
pkgver=9.4.1
_pkgver=9.4.1-P1
pkgrel=6
pkgdesc="bind named server with geoip patch can return ip depending on requester location"
arch=(i686 x86_64)
url="http://www.caraytech.com/geodns/"
license=('custom:ISC')
backup=(etc/named.conf etc/logrotate.d/named etc/conf.d/named)
depends=('openssl' 'geoip')
provides=('dns-server' 'bind' 'dnsutils')
conflicts=('bind' 'dnsutils')
options=('!libtool')
install=bind.install
source=(ftp://ftp.isc.org/isc/bind9/${_pkgver}/bind-${_pkgver}.tar.gz
	bind.so_bsdcompat.diff \
	named.conf \
	localhost.zone \
	127.0.0.zone
	named \
	root.hint \
	named.logrotate \
	named.conf.d \
	http://www.caraytech.com/geodns/bind-9.4.1-geodns-patch.tar.gz)
md5sums=('44e0514e6105ddaa235394045d9aeb0c'
         '447d58721cfee0e1e377b46f7d50b327'
         'f26d015142fa0a8226b42bbf9d490aa4'
         'ab5beef0b41eb6376c7f1f4ee233172b'
         'bdbdfe4990b0903984306dd14f98b951'
         '94f8ae2b337a4207fda88cb64f79f302'
         '8c212c0260d708f15f75d3adc71f0149'
         '91b3463a181561deb845acc122713a2a'
         '475fde63600d7d95980de991f02d6b6d'
         'd3d515bdef525f9a31787b36a105e690')

build() {
  export MAKEFLAGS="-j1"
  cd $srcdir/bind-${_pkgver}
  patch -Np0 -i $srcdir/bind.so_bsdcompat.diff
  patch -Np1 -i $srcdir/bind-9.4.1-geodns-patch/patch.diff
  ./configure --prefix=/usr --sysconfdir=/etc --localstatedir=/var \
              --with-libtool --enable-shared --disable-threads \
              --with-openssl=yes --disable-linux-caps --without-libxml2
  make LDFLAGS=-lGeoIP
  make DESTDIR=$pkgdir install
  install -D -m755 ../named $pkgdir/etc/rc.d/named
  install -D -m640 ../named.conf $pkgdir/etc/named.conf
  install -D -m600 ../127.0.0.zone $pkgdir/var/named/127.0.0.zone
  install -D -m600 ../localhost.zone $pkgdir/var/named/localhost.zone

  #grabbed from ftp://ftp.rs.internic.net/domain/named.root
  install -D -m600 ../root.hint $pkgdir/var/named/root.hint
  install -D -m600 ../named.logrotate $pkgdir/etc/logrotate.d/named
  install -D -m644 ../named.conf.d $pkgdir/etc/conf.d/named
  install -D -m644 COPYRIGHT $pkgdir/usr/share/licenses/$pkgname/LICENSE

  # adjust ownerships (our named user will be uid/gid 40/40)
  chmod 700 $pkgdir/var/named
  chown root:40 $pkgdir/etc/named.conf
  chown -R 40:40 $pkgdir/var/named

  mv $pkgdir/usr/man $pkgdir/usr/share/

  # avoid conflict with filesystem>=2012.06
  rmdir "$pkgdir/var/run"
}
