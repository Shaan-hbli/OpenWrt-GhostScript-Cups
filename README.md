## Building and installation
``` sh
# Download the OpenWRT SDK
# (you may need to select a different one, matching your device - the remaining steps are the same for all of them)
wget https://downloads.openwrt.org/releases/19.07.5/targets/ath79/generic/openwrt-sdk-19.07.5-ath79-generic_gcc-7.5.0_musl.Linux-x86_64.tar.xz
tar xvf openwrt-sdk-19.07.5-ath79-generic_gcc-7.5.0_musl.Linux-x86_64.tar.xz
cd openwrt-sdk-19.07.5-ath79-generic_gcc-7.5.0_musl.Linux-x86_64.tar.xz

# Add the package feed
echo "src-git openwrt-cups git@github.com:op4packages/openwrt-cups.git" >> feeds.conf.default

./scripts/feeds update -a
./scripts/feeds install -a


# Enable the packages
make menuconfig
# In the UI:
# Network > Printing > cups, set to M (module)
# Select Save and then Exit

# Build
# (this will take a while - it needs to build all dependencies as well)
make -j9 package/cups/compile 
# Upload to device
scp bin/packages/mips_24kc/openwrt-cups/* root@openwrt:.

# Install the packages
ssh root@router
opkg install libcups_2.3.0-2_mips_24kc.ipk
opkg install cups_2.3.0-2_mips_24kc.ipk

```
# Included packages
* libcups
  * The cups core libraries required to run cups or any drivers
* libcupsimage
  * Library to allow CUPS to work with rasterized data
* cups
  * The common unix printing system (cups) daemon itself
* cups-bsd
  * Some BSD-type utilities (lprm,lpq,lpr,lpc)
* cups-client
  * Advanced client utilities (lp,cancel,cupstestppd,ipptool,lpoptions,lpstat,cupsaccept,cupsfilter,lpadmin,lpinfo,lpmove)
* cups-filters-lite
  * Some basic printer filters (commandtops,gziptoany,pstops,rastertoepson,rastertohp,rastertolabel,rastertopwg)
* cups-ppdc
  Compiler for PPDC files (deprecated)
* cups-bjnp
  * Filter for Canon printers using canon printers using USB over IP BJNP
* cups-dymo
  * Official drivers for Dymo label printers
* cups-opfilter
  * Filters to convert JPEG, PNG or TIFF images to raster data suitable for some printers
  
# Configuring CUPS clients
Configure the printer using the following path: `ipp://openwrt:631/printers/printer_name_on_the_server`

You have to choose the driver manually.

# Configuring Windows clients

You have to choose the driver manually. In theory it's possible to serve the driver using samba but I couldn't get it to work.

# References
- [krzys-h/openwrt-samba4_with_cups](https://github.com/krzys-h/openwrt-samba4_with_cups)
- [TheMMcOfficial/lede-cups](https://github.com/TheMMcOfficial/lede-cups)
- [openwrt-19.07/net/samba4](https://github.com/openwrt/packages/tree/openwrt-19.07/net/samba4)

