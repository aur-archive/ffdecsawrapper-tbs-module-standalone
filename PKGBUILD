# Submitter:   Wessel Dirksen "p-we" <wdirksen at gmail dot com>
# Contributor: Tycho LÃ1⁄4rsen "bas-t" (responsible for hosting and development of FFdecsawrapper and more)
# Contributor: Petr Vacek "vaca" (contributing code from sascng-linux3-patch and cardslot.conf for serial port SC readers)
# Contributor: J.P. van Best (implementing new procfs API for kernels >= 3.10 in FFdecsawrapper kernel module)
# Contributor: Oliver Endress (providing improved mutex patch for dvbdev.c)

## !! This package can only be used with ffdecsawrapper-git-tbs pre-installed and working

pkgname=ffdecsawrapper-tbs-module-standalone
pkgver=v150429
pkgrel=1
pkgdesc="FFdecsa empowered softcam - Standalone TBS drivers + loopback module only"
url=https://github.com/bas-t/ffdecsawrapper.git
arch=('i686' 'x86_64')
license=('GPLv3')
depends=('ffdecsawrapper-git-tbs')
makedepends=('git' 'linux-headers')
conflicts=('ffdecsawrapper-tbs-module-standalone')
provides=('ffdecsawrapper-tbs-module-standalone')
install='ffdecsawrapper-module-standalone.install'

_tbsver=v150429

# !! You must use the same ffdecsawrapper branch in the line below as with ffdecsawrapper-git:

source=('git://github.com/bas-t/ffdecsawrapper.git#branch=stable' \
	"http://www.tbsdtv.com/download/document/common/tbs-linux-drivers_$_tbsver.zip" \
	'ffdecsawrapper-module-standalone.install' 'tbs-mutex2.patch')
	 
sha256sums=('SKIP'
            'fdc905866a01231595e23c53b7b7b5e81428c10844215c1be1231c4a1297f743'
            '16c36bf8bdbe4281c950c61bf5ac49b90b766f1341becd0ac4b5dc547e2b18d6'
            '93adfa959706848c5ca44e5c15da3539ff3b02082a72f912a94804bf3676fb28')

pkgver() {

	cd $srcdir/ffdecsawrapper
	_gitffdecsawrapper=`git describe --always | sed 's|-|.|g'`
	_kernel=`uname -r | sed -r 's/-/_/g'`
	echo "$_gitffdecsawrapper"_"$_tbsver"_"$_kernel"
}

prepare() {

	cd $srcdir
	rm -rf /linux-tbs-drivers
	tar xjvf linux-tbs-drivers.tar.bz2
	chmod 777 -R $srcdir/linux-tbs-drivers
	cd $srcdir/linux-tbs-drivers
	make distclean

		if [ `uname -m` == "x86_64" ]; then
			./v4l/tbs-x86_64.sh  
		else
			./v4l/tbs-x86_r3.sh
		fi

	cd $srcdir/linux-tbs-drivers
	msg "Applying tbs-mutex-patch2..."
	patch -p1 < $srcdir/tbs-mutex2.patch
	sleep 4
}

build() {

	cd $srcdir/linux-tbs-drivers
	make

	cd $srcdir/ffdecsawrapper      
	./configure --dvb_dir=$srcdir/linux-tbs-drivers/linux --update=no
}

package() {

	mkdir -p $pkgdir/usr/lib/modules/`uname -r`/updates/{tbs,ffdecsawrapper}
	install -m0755 $srcdir/ffdecsawrapper/dvbloopback.ko  $pkgdir/usr/lib/modules/`uname -r`/updates/ffdecsawrapper
	find "$srcdir/linux-tbs-drivers" -name '*.ko' -exec cp {} $pkgdir/usr/lib/modules/`uname -r`/updates/tbs \;

	msg "Compressing modules, this will take awhile..."
	find "$pkgdir" -name '*.ko' -print0 | xargs -0 -P`nproc` -n10 gzip -9

	chmod 755 -R $pkgdir/usr/lib/modules/`uname -r`/updates
}

