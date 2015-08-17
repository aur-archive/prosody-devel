# Maintainer: Dwayne Bent <dbb@dbb.io>
# Maintainer: Sergej Pupykin <pupykin.s+arch@gmail.com>
# Contributor: Paul-Sebastian Manole <brokenthorn@gmail.com>

pkgname=prosody-devel
pkgver=0.9.0rc5
pkgrel=1
pkgdesc="Lightweight and extensible Jabber/XMPP server written in Lua."
arch=('i686' 'x86_64')
url="http://prosody.im/"
license=('MIT')
depends=('lua51' 'lua51-socket' 'lua51-expat' 'lua51-filesystem' 'libidn' 'openssl')
optdepends=('lua51-sec: TLS encryption support')
conflicts=('prosody')
provides=('prosody')
install=prosody.install
backup=('etc/logrotate.d/prosody'
	'etc/prosody/prosody.cfg.lua')
source=("http://prosody.im/tmp/${pkgver}/prosody-${pkgver}.tar.gz"
	'prosody.logrotated'
	'fix-config.patch'
	'prosody.tmpfile.d'
	'prosody.service')
md5sums=('2075effe3c4d014b54680f4a497d1b9a'
         '5b44aee99f1fa9e7f055e067688cafbd'
         '827a3be58582dbbd9f0a427560b45797'
         'e2f5a1df410b05696a30dcb058841084'
         'e74045f27cb60908d535969906781f75')

build() {
    cd "$srcdir/prosody-$pkgver"
    patch -p1 <$srcdir/fix-config.patch
    find . -type f -exec \
      sed -i '1s,^#! \?/usr/bin/\(env \|\)lua$,#!/usr/bin/lua5.1,' {} \;
    ./configure --prefix=/usr --sysconfdir=/etc/prosody \
        --datadir=/var/lib/prosody --with-lua-include=/usr/include/lua5.1
    make
}

package() {
    cd "$srcdir/prosody-$pkgver"

    make DESTDIR="$pkgdir" install
    make DESTDIR="$pkgdir" install -C tools/migration

    rm $pkgdir/etc/prosody/certs/*

    install -d "$pkgdir/etc/logrotate.d"
    install -d "$pkgdir/var/log/prosody"
    for i in tools/*.lua; do
      install -Dm0644 $i $pkgdir/usr/share/prosody/$i
    done

    install -o root -g root -m 644 "$srcdir/prosody.logrotated" \
        "$pkgdir/etc/logrotate.d/prosody"

    install -Dm0644 COPYING $pkgdir/usr/share/licenses/$pkgname/COPYING
    install -Dm0644 $srcdir/prosody.tmpfile.d $pkgdir/usr/lib/tmpfiles.d/prosody.conf
    install -Dm0644 $srcdir/prosody.service $pkgdir/usr/lib/systemd/system/prosody.service
}

