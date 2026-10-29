# OpenWrt 25.12 for Cudy LT300 v3

Custom OpenWrt 25.12 image tailored for the **Cudy LT300 v3** (MediaTek MT7628AN, 64MB RAM, 16MB SPI Flash).

Dirt-cheap hardware, but carries a well-documented, highly flexible internal modem with full AT command access (IMEI change, raw cellular telemetry, SMS). Built specifically to serve as a reliable LTE gateway and lightweight automation box.

### Pre-installed Packages
* `sms-tool`
* `curl`
* `luci-app-sqm`
* `python3-light`
* `watchcat`

### Wi-Fi Backports & Fixes (compared to stock OpenWrt 25.12)
Includes upstream backports for `mac80211` and an updated `mt76` tree with key `mt7603` fixes.

> **Note:** only changes to the `mt7603` driver (used by the MT7628 Wi-Fi) are listed here. The `mt76` update also pulls in upstream changes to the mt76 core and to other chips, which are not listed. Changes to the OpenWrt tree itself are listed in the next section.

#### Upstream fixes
Merged in [openwrt/mt76](https://github.com/openwrt/mt76), but not part of stock 25.12:
* Proper TID frame buffering reported to `mac80211`
* Power-save & U-APSD fixes (hardware PS buffering restoration, queue empty reporting, proper EOSP bit handling)
* Fixed beacon SKB leak on tx error
* Added support for `0x7592` EEPROM chip ID
* Short frames looped back on power-save entry are no longer dropped
* Global station WCID is now initialized

#### Custom fixes
Only in this fork, not part of upstream mt76:
* Switched to RCPI for accurate RSSI calculation (dropped broken `rssi_offset`)
* Force SMPS mode to be reapplied on association
* Dynamic EDCA params update on slot time change events
* Removed dead WEP handling code from mt7603 key management
* Added hwmon temperature sensor (`temp1_input`, read from the chip's thermal ADC with the factory EEPROM calibration)

### Changes in this repository (compared to stock OpenWrt 25.12)
* `mt76`: source switched to the [kidala123/mt76](https://github.com/kidala123/mt76) fork, pinned to `4c00063f` (2026-10-17) instead of `39c960c3` (2026-03-19). Mirror hash check is disabled (`PKG_MIRROR_HASH:=skip`)
* `mt76`: removed the stock backport patches from `package/kernel/mt76/patches`, since the fork already includes them. Three of them are not in the fork, and none affects the MT7628: mt7996 hif2 WED, mt7921 CLC records, Airoha NPU dependency tracking
* `mac80211`: backported 5 patches from OpenWrt main that the newer `mt76` needs to build: `ieee80211_txq_aql_pending()`, interface and station iteration helpers, `assoc_link_id` reporting for non-MLD stations, airtime weight change notification
* Build config for the Cudy LT300 v3 is tracked in git (`.config`)
* `video` feed disabled in `feeds.conf.default`
* Removed the `/etc/profile.d/apk-cheatsheet.sh` login banner (`opkg` to `apk` hints)

---

![OpenWrt logo](include/logo.png)

OpenWrt Project is a Linux operating system targeting embedded devices. Instead
of trying to create a single, static firmware, OpenWrt provides a fully
writable filesystem with package management. This frees you from the
application selection and configuration provided by the vendor and allows you
to customize the device through the use of packages to suit any application.
For developers, OpenWrt is the framework to build an application without having
to build a complete firmware around it; for users this means the ability for
full customization, to use the device in ways never envisioned.

Sunshine!

## Download

Built firmware images are available for many architectures and come with a
package selection to be used as WiFi home router. To quickly find a factory
image usable to migrate from a vendor stock firmware to OpenWrt, try the
*Firmware Selector*.

* [OpenWrt Firmware Selector](https://firmware-selector.openwrt.org/)

If your device is supported, please follow the **Info** link to see install
instructions or consult the support resources listed below.

## 

An advanced user may require additional or specific package. (Toolchain, SDK, ...) For everything else than simple firmware download, try the wiki download page:

* [OpenWrt Wiki Download](https://openwrt.org/downloads)

## Development

To build your own firmware you need a GNU/Linux, BSD or macOS system (case
sensitive filesystem required). Cygwin is unsupported because of the lack of a
case sensitive file system.

### Requirements

You need the following tools to compile OpenWrt, the package names vary between
distributions. A complete list with distribution specific packages is found in
the [Build System Setup](https://openwrt.org/docs/guide-developer/build-system/install-buildsystem)
documentation.

```
binutils bzip2 diff find flex gawk gcc-6+ getopt grep install libc-dev libz-dev
make4.1+ perl python3.7+ rsync subversion unzip which
```

### Quickstart

1. Run `./scripts/feeds update -a` to obtain all the latest package definitions
   defined in feeds.conf / feeds.conf.default

2. Run `./scripts/feeds install -a` to install symlinks for all obtained
   packages into package/feeds/

3. Run `make menuconfig` to select your preferred configuration for the
   toolchain, target system & firmware packages.

4. Run `make` to build your firmware. This will download all sources, build the
   cross-compile toolchain and then cross-compile the GNU/Linux kernel & all chosen
   applications for your target system.

### Related Repositories

The main repository uses multiple sub-repositories to manage packages of
different categories. All packages are installed via the OpenWrt package
manager called `opkg`. If you're looking to develop the web interface or port
packages to OpenWrt, please find the fitting repository below.

* [LuCI Web Interface](https://github.com/openwrt/luci): Modern and modular
  interface to control the device via a web browser.

* [OpenWrt Packages](https://github.com/openwrt/packages): Community repository
  of ported packages.

* [OpenWrt Routing](https://github.com/openwrt/routing): Packages specifically
  focused on (mesh) routing.

* [OpenWrt Video](https://github.com/openwrt/video): Packages specifically
  focused on display servers and clients (Xorg and Wayland).

## Support Information

For a list of supported devices see the [OpenWrt Hardware Database](https://openwrt.org/supported_devices)

### Documentation

* [Quick Start Guide](https://openwrt.org/docs/guide-quick-start/start)
* [User Guide](https://openwrt.org/docs/guide-user/start)
* [Developer Documentation](https://openwrt.org/docs/guide-developer/start)
* [Technical Reference](https://openwrt.org/docs/techref/start)

### Support Community

* [Forum](https://forum.openwrt.org): For usage, projects, discussions and hardware advise.
* [Support Chat](https://webchat.oftc.net/#openwrt): Channel `#openwrt` on **oftc.net**.

### Developer Community

* [Bug Reports](https://bugs.openwrt.org): Report bugs in OpenWrt
* [Dev Mailing List](https://lists.openwrt.org/mailman/listinfo/openwrt-devel): Send patches
* [Dev Chat](https://webchat.oftc.net/#openwrt-devel): Channel `#openwrt-devel` on **oftc.net**.

## License

OpenWrt is licensed under GPL-2.0
