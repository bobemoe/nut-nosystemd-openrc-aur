# nut-nosystemd-openrc

**Network UPS Tools - systemd-free version for OpenRC systems**

## Description

This is a custom build of [Network UPS Tools (NUT)](https://networkupstools.org/) specifically designed for systemd-free Arch Linux systems using OpenRC as the init system. This package conflicts with and replaces the official `nut` package.

## Key Differences from Official NUT

- **Systemd-free configuration**: Built with `--with-systemdsystemunitdir=no --with-libsystemd=no`
- **OpenRC service scripts**: Includes custom `nut`, `upsd`, and `upsmon` init scripts for OpenRC
- **Full feature set**: Includes CGI web interface and all standard NUT functionality

## Build Configuration

This package uses the systemd-free build flags recommended in [NUT GitHub issue #2754](https://github.com/networkupstools/nut/issues/2754):

```bash
./configure \
    --with-systemdsystemunitdir=no \
    --with-libsystemd=no \
    --with-cgipath=/usr/share/nut/cgi \
    --with-dev --with-doc=man --with-libltdl \
    --with-neon --with-openssl --with-serial \
    --with-snmp --with-usb \
    --without-avahi --without-ipmi \
    --without-freeipmi --without-powerman \
    --without-wrap
```

## OpenRC Service Management

After installation, manage NUT services with:

```bash
# Enable services
sudo rc-update add nut default

# Start/stop services  
sudo rc-service nut start
sudo rc-service nut stop

# Check status
sudo rc-service nut status
```

## Installation

Install from AUR using your preferred AUR helper (not submitted yet) or manually:

```bash
git clone https://github.com/bobemoe/nut-nosystemd-openrc-aur
cd nut-nosystemd-openrc-aur
makepkg -si
```

## Credits

- Build configuration based on [NUT GitHub issue #2754](https://github.com/networkupstools/nut/issues/2754)
- [Network UPS Tools project](https://networkupstools.org/) for the excellent UPS monitoring software
- Original NUT maintainers and contributors

For usage, configuration, and troubleshooting, refer to the [official NUT documentation](https://networkupstools.org/documentation.html).
