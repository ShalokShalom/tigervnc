pkgname=tigervnc
pkgver=1.6.0
pkgrel=1
_xorgver=1.18.0
pkgdesc="Suite of VNC servers and clients. Based on the VNC 4 branch of TightVNC."
arch=('x86_64')
url="http://www.tigervnc.org"
license=('GPL')
depends=('fltk' 'pam' 'gnutls' 'libjpeg-turbo' 'libxtst' 'libxfont' 'pixman'
	 'xorg-xauth' 'xkeyboard-config'
	 'libgl' 'libgcrypt' 'perl' 'libxdamage' 'xorg-xkb-utils' 'xorg-server-utils')
makedepends=('cmake' 'nasm' 'xorg-font-util' 'xorg-util-macros' 'bigreqsproto'
	     'compositeproto' 'damageproto' 'randrproto' 'resourceproto'
	     'scrnsaverproto' 'videoproto' 'xcmiscproto' 'xf86vidmodeproto'
	     'xtrans' 'glproto' 'dri2proto' 'dri3proto' 'presentproto'
	     'mesa' 'imagemagick')
optdepends=('mesa: for OpenGL functionality in Xvnc')
source=("$pkgname-$pkgver.tar.gz::https://github.com/TigerVNC/tigervnc/archive/v${pkgver}.tar.gz"
	"ftp://ftp.freedesktop.org/pub/xorg/individual/xserver/xorg-server-${_xorgver}.tar.bz2"
	"vncserver.service"
	"vncviewer.desktop"
	"gethomedir.patch"
	"xorg118.patch")
md5sums=('78b736445781d86c48e942465a391ccc'
         '3c1c1057d3ad27380d8dd87ffcc182cd'
         'eede8fd55bc2fab3d5207d33d76fc2da'
         'b200d83c60e80c6f9693ea19a2d9f5b0'
         'f5821bb8f9d3d9ec04d27abbb99e7344'
         '28cdd5534f4b8eb5e08a0fc04b1fc543')

prepare() {
  cd ${srcdir}/${pkgname}-${pkgver}
  patch -Np1 -i ${srcdir}/xorg118.patch
  patch -Np1 -i ${srcdir}/gethomedir.patch
  sed -i 's/iconic/nowin/' unix/vncserver
  cd unix/xserver
  cp -r ${srcdir}/xorg-server-${_xorgver}/* .
  patch -Np1 -i ../xserver117.patch
}

build() {
    cd ${srcdir}/${pkgname}-${pkgver}
  cmake -G "Unix Makefiles" -DCMAKE_INSTALL_PREFIX=/usr
  make
  cd unix/xserver
  autoreconf -fiv
  ./configure --prefix=/usr \
	--disable-static --without-dtrace \
	--disable-xorg --disable-xnest --disable-xvfb --disable-dmx \
	--disable-xwin --disable-xephyr --disable-kdrive --disable-xwayland \
	--disable-config-hal --disable-config-udev --with-pic \
	--disable-unit-tests --disable-devel-docs --disable-selective-werror \
	--disable-dri --enable-dri2 --enable-dri3 --enable-glx --enable-glx-tls
  make
}

package() {
  cd ${srcdir}/${pkgname}-${pkgver}
  make DESTDIR=${pkgdir} install
  cd unix/xserver/hw/vnc
  make DESTDIR=${pkgdir} install
  install -Dm0644 ${srcdir}/${pkgname}-${pkgver}/contrib/systemd/user/vncserver@.service \
    $pkgdir/usr/lib/systemd/user/vncserver@.service
  install -Dm0644 $srcdir/vncserver.service $pkgdir/usr/lib/systemd/system/vncserver.service
  install -Dm0644 $srcdir/vncviewer.desktop $pkgdir/usr/share/applications/vncviewer.desktop
}
