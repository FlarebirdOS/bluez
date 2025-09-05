pkgname=bluez
pkgver=5.83
pkgrel=1
pkgdesc="Daemons for the bluetooth protocol stack"
arch=('x86_64')
url="http://www.bluez.org"
license=('GPL-2.0-only')
depends=(
    'dbus'
    'glib2'
    'glibc'
    'json-c'
    'libical'
    'readline'
    'systemd-libs'
)
makedepends=(
    'python-docutils'
    'systemd'
)
backup=(
    etc/bluetooth/main.conf
    etc/bluetooth/input.conf
    etc/bluetooth/network.conf
    etc/bluetooth/rfcomm.conf
    etc/bluetooth/uart.conf
)
source=(https://www.kernel.org/pub/linux/bluetooth/${pkgname}-${pkgver}.tar.xz
    bluetooth.modprobe)
sha256sums=(108522d909d220581399bfec93daab62035539ceef3dda3e79970785c63bd24c
    46c021be659c9a1c4e55afd04df0c059af1f3d98a96338236412e449bf7477b4)

build() {
    cd ${pkgname}-${pkgver}

    local configure_args=(
        --sysconfdir=/etc
        --localstatedir=/var
        ${configure_options}
    )

    ./configure "${configure_args[@]}"

    make
}

package() {
    cd ${pkgname}-${pkgver}

    make DESTDIR=${pkgdir} install

    install -vdm755 ${pkgdir}/usr/sbin
    ln -svf ../libexec/bluetooth/bluetoothd ${pkgdir}/usr/sbin

    install -v -dm755 ${pkgdir}/etc/bluetooth
    install -v -m644 src/main.conf ${pkgdir}/etc/bluetooth/main.conf

    cat > ${pkgdir}/etc/bluetooth/rfcomm.conf << "EOF"
#
# /etc/bluetooth/rfcomm.conf
#

# Set up the RFCOMM configuration of the Bluetooth subsystem in the Linux kernel.
# Use one line per command
# See the rfcomm man page for options


EOF

    cat > ${pkgdir}/etc/bluetooth/uart.conf << "EOF"
#
# /etc/bluetooth/uart.conf
#

# Attach serial devices via UART HCI to BlueZ stack
# Use one line per device
# See the hciattach man page for options

EOF

    install -v -dm755 ${pkgdir}/usr/share/doc/${pkgname}-${pkgver}
    install -v -m644 doc/*.txt ${pkgdir}/usr/share/doc/${pkgname}-${pkgver}

    # fix module loading errors
    install -Dm644 ${srcdir}/bluetooth.modprobe -t ${pkgdir}/usr/lib/modprobe.d/bluetooth-usb.conf

    # load module at system start required by some functions
    # https://bugzilla.kernel.org/show_bug.cgi?id=196621
    install -dm755 ${pkgdir}/usr/lib/modules-load.d
    echo "crypto_user" > ${pkgdir}/usr/lib/modules-load.d/bluez.conf
}

