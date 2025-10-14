# Maintainer: Zach Hoffman <zach@zrhoffman.net>
pkgname=f5vpn
pkgver=7261.2025.1009.1
pkgrel=1
pkgdesc='VPN client using the Point-to-Point Protocol to connect to F5Networks BIG-IP APM'
arch=(aarch64 x86_64)
mirror=vpn-mgmt.it.mtu.edu
source=('LICENSE'
        'no-desktop-file-dbus.aarch64.patch'
        'no-desktop-file-dbus.x86_64.patch')
source_aarch64=("linux_${pkgname}-${pkgver}.aarch64.deb::https://${mirror}/public/download/linux_${pkgname}.aarch64.deb")
source_x86_64=("linux_${pkgname}-${pkgver}.x86_64.deb::https://${mirror}/public/download/linux_${pkgname}.x86_64.deb")
b2sums_aarch64=('096b8c500720e4aca54226b7d6df0b28db416edbd1955be326a3e47dd0c023cdea1e879200ee621e74ef1b5c7852bba81284e8bd5d41774bc0f43fcfbfebb458')
b2sums_x86_64=('3fc1523b39729199a61a6f40c0a3c2f7a073bba7610f36f9fe5c624f52ba0cadee4df695f26a08f05ceb3452c4de1ad3154e919c641c0e3dbcb8f840f9b4185f')
b2sums=('c864e69799ffc4c13bf8af7d76bcc8beed195d9a41acb01d459aaa0c3cd5bae75290ef7be1fa6a0bfd6472e1c3f8df3a7f5d59767861ded0dc4a373a3ead447c'
'd64f0d1a4d413701dd31464c086b84049fbf5919774f2440a8d9f139deedf8b9e83a229ad59cf5434c5ccd33a0151bb9621d941096b4cc1d641c86681f81eafa'
'5be3f5ebd0358e022c85081b45bc23b1e847542ae9035bece95c40fa5113a1fd9159ced013950d237206c2e6be94caa38b9d6414ea29ab65cd80043a2dfbfe5a')
sha256sums_aarch64=('98aa84c110de232457292bee34f9709c12fa7a3ff3602d707373c266a6d19f88')
sha256sums_x86_64=('2ca93337ea1042df11e5d35bf3903f05ff1d70e8ae26d148d686fff7cad0a714')
sha256sums=('85f06be8b8e438c4cefdad9e8975d1c48fd53446fe35e95d4260ba14ac7f98fd'
'fadc7ae9c2297a93101a98c24ed63087a05a6e24bb33ba4b795bfd4339dba7cf'
'3ecd8a10941a7d81b9d16aecdf6ac9caa1f957e32a2d2c65bde5181abd1fcb73')
makedepends=(qt5-base)
depends=(libxml2-legacy openssl qt5-base)
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
      if [[ "$library" == lib/libicu* || "$library" == lib/libQt5WebKit* ]]; then
        continue
      fi
      ln -sf "$(<<<"/usr/${library%%.so.*}.so" sed 's|/usr/lib|/usr/lib64|g')" "$library"
  done

  # Use system Qt libraries
  for plugin in platforms/*.so; do
      ln -sf "/usr/lib64/qt/plugins/${plugin}" "$plugin"
  done

  for resolution in 16 24 32 48 64 96 128 256 512 1024; do
      install -Dm644 "logos/${resolution}x${resolution}.png" \
                      "${pkgdir}/usr/share/icons/hicolor/${resolution}x${resolution}/apps/${pkgname}.png"
  done
  )
  install -Dm644 'LICENSE' "${pkgdir}/usr/share/licenses/${pkgname}/LICENSE"
  cp -a opt "${pkgdir}"
}
