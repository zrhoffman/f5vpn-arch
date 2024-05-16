# Maintainer: Zach Hoffman <zach@zrhoffman.net>
pkgname=f5vpn
pkgver=7246.2024.0201.1
_pkgver_aarch64=7245.2023.1006.1
pkgrel=4
pkgdesc='VPN client using the Point-to-Point Protocol to connect to F5Networks BIG-IP APM'
arch=(aarch64 x86_64)
mirror=vpn.chapman.edu
mirror_aarch64=remote.abdn.ac.uk
source=('LICENSE'
        'no-desktop-file-dbus.aarch64.patch'
        'no-desktop-file-dbus.x86_64.patch')
source_aarch64=("linux_${pkgname}-${_pkgver_aarch64}.aarch64.deb::https://${mirror_aarch64}/public/download/linux_${pkgname}.aarch64.deb")
source_x86_64=("linux_${pkgname}-${pkgver}.x86_64.deb::https://${mirror}/public/download/linux_${pkgname}.x86_64.deb")
b2sums_aarch64=('56020ea5281c5c3305055dd88c5c378231765bdacc1c41d2539ff66ea94cfe82ec38bb482137a6aa20e9ff147c12163f3a81ec34b65590d6a3c67c012eaf9199')
b2sums_x86_64=('7d101638f437bce3f309670765c2de8bdad47d4b01a0b24dd741aabf68625c55d39cf2a4700514f622a7ff8da3f88353e2013f9b463310e58e83408819dd687e')
b2sums=('c864e69799ffc4c13bf8af7d76bcc8beed195d9a41acb01d459aaa0c3cd5bae75290ef7be1fa6a0bfd6472e1c3f8df3a7f5d59767861ded0dc4a373a3ead447c'
'd64f0d1a4d413701dd31464c086b84049fbf5919774f2440a8d9f139deedf8b9e83a229ad59cf5434c5ccd33a0151bb9621d941096b4cc1d641c86681f81eafa'
'5be3f5ebd0358e022c85081b45bc23b1e847542ae9035bece95c40fa5113a1fd9159ced013950d237206c2e6be94caa38b9d6414ea29ab65cd80043a2dfbfe5a')
sha256sums_aarch64=('56c3511bed396241210484a1e14c251e93ce156c89c8a5964439a4628505483c')
sha256sums_x86_64=('5088f827a4e338f31588aa5b19823ebf40376fa043bee09b49c59bace112e6c5')
sha256sums=('85f06be8b8e438c4cefdad9e8975d1c48fd53446fe35e95d4260ba14ac7f98fd'
'fadc7ae9c2297a93101a98c24ed63087a05a6e24bb33ba4b795bfd4339dba7cf'
'3ecd8a10941a7d81b9d16aecdf6ac9caa1f957e32a2d2c65bde5181abd1fcb73')
depends=('icu>=73.2-2' openssl qt5-base 'qt5-webkit>=5.212.0alpha4-22')
provides=("${pkgname}")
url='https://support.f5.com/csp/article/K32311645#link_04_05'
license=('commercial')

pkgver() {
  tar -xf data.tar.gz
  grep -oEm1 --text '([0-9]{4}\.){3}[0-9]+' opt/f5/vpn/svpn
}

package() {
  (
  cd "${srcdir}/opt/f5/vpn"

  patch -i "${srcdir}/no-desktop-file-dbus.${CARCH}.patch" # Desktop file does not work with Dbus enabled

  chmod u+s svpn # f5vpn should not be run as root, but it calls svpn which must be run as root
  install -Dm644 "com.f5.${pkgname}.desktop" "${pkgdir}/usr/share/applications/com.f5.${pkgname}.desktop"
  install -dm755 "${pkgdir}/usr/bin/"
  install -dm755 "${pkgdir}/usr/local/lib/F5Networks/SSLVPN/var/run" # For svpn.pid
  ln -s '/opt/f5/vpn/f5vpn_launch_helper.sh' "${pkgdir}/usr/bin/${pkgname}"
  ln -s '/opt/f5/vpn/tunnelserver' "${pkgdir}/usr/bin/tunnelserver"

  # Use system Qt libraries
  for library in lib/*.so.*; do
      ln -sf "/usr/${library%%.so.*}.so" "$library"
  done

  # Use system Qt libraries
  for plugin in platforms/*.so; do
      ln -sf "/usr/lib/qt/plugins/${plugin}" "$plugin"
  done

  for resolution in 16 24 32 48 64 96 128 256 512 1024; do
      install -Dm644 "logos/${resolution}x${resolution}.png" \
                      "${pkgdir}/usr/share/icons/hicolor/${resolution}x${resolution}/apps/${pkgname}.png"
  done
  )
  install -Dm644 'LICENSE' "${pkgdir}/usr/share/licenses/${pkgname}/LICENSE"
  cp -a opt "${pkgdir}"
}
