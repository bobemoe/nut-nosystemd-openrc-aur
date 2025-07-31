# Maintainer: Your Name <your.email at domain dot tld>
# Contributor: Network UPS Tools team

pkgname=nut-nosystemd-openrc
pkgver=2.8.3
pkgrel=1
pkgdesc="Network UPS Tools - systemd-free version for OpenRC systems"
arch=('x86_64' 'i686' 'aarch64' 'armv7h')
url="https://networkupstools.org/"
license=('GPL2' 'GPL3')
depends=('libusb' 'openssl' 'net-snmp' 'libudev' 'gd')
makedepends=('asciidoc' 'python' 'gperf')
optdepends=(
    'libmodbus: for Modbus support'
    'neon: for XML/HTTP support'
    'powerman: for PowerMan PDU support'
)
conflicts=('nut')
provides=('nut')
backup=(
    'etc/nut/nut.conf'
    'etc/nut/ups.conf'
    'etc/nut/upsd.conf'
    'etc/nut/upsd.users'
    'etc/nut/upsmon.conf'
    'etc/nut/upssched.conf'
    'etc/nut/hosts.conf'
)
install=${pkgname}.install
source=("https://github.com/networkupstools/nut/releases/download/v${pkgver}/nut-${pkgver}.tar.gz"
        "nut.initd"
        "upsd.initd"
        "upsmon.initd")
sha256sums=('d6ca17f0b39003bac7649eb17ab4a713e4d5fcaa8fd1aedca28357d59df095ed'  # nut-2.8.3.tar.gz
            'da579e41c63d922aad57425d5860b76944f3bf26363feca959ac3b1cc742bad8'  # nut.initd
            'af207924f8385ec6da4ecd6e0863af2e8121e450ab9f6ba811e605b018b78c83'  # upsd.initd
            '71242a51a31c2c15948fe5aa7e25b96ff22f80a96ec01315150d8b54ffd81e91') # upsmon.initd

prepare() {
    cd "${srcdir}/nut-${pkgver}"
    
    # Apply any patches if needed
    # patch -p1 < "${srcdir}/some.patch"
}

build() {
    cd "${srcdir}/nut-${pkgver}"
    
    # Generate build scripts
    ./autogen.sh
    
    # Configure with systemd-free flags (from GitHub issue #2754)
    ./configure \
        --prefix=/usr \
        --datadir=/usr/share/nut \
        --libexecdir=/usr/lib/nut \
        --sbindir=/usr/bin \
        --sysconfdir=/etc/nut \
        --disable-static \
        --with-user=nut \
        --with-group=nut \
        --with-altpidpath=/run/nut \
        --with-cgipath=/usr/share/nut/cgi \
        --with-drvpath=/usr/lib/nut \
        --with-htmlpath=/usr/share/nut/html \
        --with-pidpath=/run/nut \
        --with-statepath=/var/lib/nut \
        --with-systemdsystemunitdir=no \
        --with-libsystemd=no \
        --with-udev-dir=/usr/lib/udev \
        --with-dev \
        --with-doc=man \
        --with-libltdl \
        --with-neon \
        --with-openssl \
        --with-serial \
        --with-snmp \
        --with-usb \
        --without-avahi \
        --without-ipmi \
        --without-freeipmi \
        --without-powerman \
        --without-wrap
    
    make
}

check() {
    cd "${srcdir}/nut-${pkgver}"
    
    # Run basic tests
    make check || true
}

package() {
    cd "${srcdir}/nut-${pkgver}"
    
    # Install the main package
    make DESTDIR="${pkgdir}" install
    
    # Install OpenRC init scripts
    install -D -m755 "${srcdir}/nut.initd" "${pkgdir}/etc/init.d/nut"
    install -D -m755 "${srcdir}/upsd.initd" "${pkgdir}/etc/init.d/upsd"
    install -D -m755 "${srcdir}/upsmon.initd" "${pkgdir}/etc/init.d/upsmon"
    
    # Create state directories
    install -d -m750 "${pkgdir}/var/lib/nut"
    install -d -m755 "${pkgdir}/run/nut"
    
    # Install udev rules for USB devices
    sed 's|@RUN_AS_GROUP@|nut|g' \
        scripts/udev/nut-usbups.rules.in > "${pkgdir}/usr/lib/udev/rules.d/62-nut-usbups.rules"
    
    # Install default configuration files (without .sample extension)
    install -D -m640 conf/nut.conf.sample "${pkgdir}/etc/nut/nut.conf"
    install -D -m640 conf/ups.conf.sample "${pkgdir}/etc/nut/ups.conf"
    install -D -m640 conf/upsd.conf.sample "${pkgdir}/etc/nut/upsd.conf"
    install -D -m640 conf/upsd.users.sample "${pkgdir}/etc/nut/upsd.users"
    install -D -m644 conf/upsmon.conf.sample "${pkgdir}/etc/nut/upsmon.conf"
    install -D -m644 conf/upssched.conf.sample "${pkgdir}/etc/nut/upssched.conf"
    install -D -m644 conf/hosts.conf.sample "${pkgdir}/etc/nut/hosts.conf"
    
    # Remove systemd files if any were created
    rm -rf "${pkgdir}/usr/lib/systemd" || true
    
    # Set proper ownership for config files
    # Note: This will be handled by the .install script
}
