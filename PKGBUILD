# Maintainer: edward-p <edward At edward-p Dot xyz>

_install_name=proxmark3
pkgbase=proxmark3-iceman-git
pkgname=($pkgbase'-generic' $pkgbase'-rdv4')
pkgver=6885.3408d75a
pkgrel=1
pkgdesc='A powerful general purpose RFID tool, the size of a deck of cards, designed to snoop, listen and emulate everything from Low Frequency (125kHz) to High Frequency (13.56MHz) tags. (iceman fork)'
arch=('x86_64')
url='https://github.com/RfidResearchGroup/proxmark3'
license=('GPL2')
depends=('libusb' 'perl' 'qt5-base' 'ncurses')
makedepends=('git' 'arm-none-eabi-gcc' 'arm-none-eabi-newlib')
provides=('proxmark3' 'proxmark3-iceman')
conflicts=('proxmark3' 'proxmark3-iceman')
source=("$pkgbase::git+https://github.com/RfidResearchGroup/proxmark3.git")
sha512sums=('SKIP')
install=proxmark3-iceman-git.install

pkgver() {
  cd $pkgbase
  echo $(git rev-list --count HEAD).$(git rev-parse --short HEAD)
}

prepare() {
  cp -r $pkgbase $pkgbase-generic
}

build() {
  cd "${srcdir}/${pkgbase}"
  make PLATFORM=PM3RDV4 PLATFORM_EXTRAS=BTADDON

  for obj in $(find | grep -E '\.o$'); do
    rm $obj
  done

  cd "${srcdir}/${pkgbase}-generic"
  make PLATFORM=PM3OTHER

  for obj in $(find | grep -E '\.o$'); do
    rm $obj
  done
}

_package() {

  install -Dm 644 "driver/77-pm3-usb-device-blacklist.rules" "$pkgdir/usr/lib/udev/rules.d/77-pm3-usb-device-blacklist.rules"
  
  install -dm 755 "$pkgdir/usr/bin"
  install -dm 755 "$pkgdir/usr/share/$_install_name"
  install -dm 755 "$pkgdir/usr/share/doc/$_install_name/"

  cp -ar doc/* "$pkgdir/usr/share/doc/$_install_name/"

  install -Dm 644 -t "$pkgdir/usr/share/doc/$_install_name/" README.md HACKING.md \
    COMPILING.txt CHANGELOG.md
  
  install -Dm 644 LICENSE.txt "$pkgdir/usr/share/licenses/$_install_name/LICENSE.txt"

  rm -rf *.txt *.md doc/

  cp -a * "$pkgdir/usr/share/$_install_name/"

  cat > "$pkgdir/usr/bin/$_install_name" << EOF
#!/bin/sh
exec /usr/share/$_install_name/client/proxmark3 "\$@"
EOF

  chmod +x "$pkgdir/usr/bin/$_install_name"

  cat > "$pkgdir/usr/bin/$_install_name-flasher" << EOF
#!/bin/sh
exec /usr/share/$_install_name/client/flasher "\$@"
EOF

  chmod +x "$pkgdir/usr/bin/$_install_name-flasher"

  cat > "$pkgdir/usr/bin/$_install_name-unbind-$_install_name" << EOF
#!/bin/sh
exec /usr/share/$_install_name/client/unbind-$_install_name "\$@"
EOF

  chmod +x "$pkgdir/usr/bin/$_install_name-unbind-$_install_name"
}

package_proxmark3-iceman-git-generic(){
    conflicts+=($pkgbase'-rdv4')
    cd "${srcdir}/${pkgbase}-generic"
    _package
}

package_proxmark3-iceman-git-rdv4(){
    conflicts+=($pkgbase'-generic')
    cd "${srcdir}/${pkgbase}"
    _package
}
