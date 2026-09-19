# bsp-imx95-var-dart

Board support extension for the **Variscite DART-MX95** SOM on the
**Sonata-Board** devkit, target `imx95-var-dart`.

The Yocto machine (`avocado-imx95-var-dart`, in meta-avocado) builds the SOM:
u-boot-variscite, the linux-variscite-imx kernel, the boot firmware chain
(imx-boot / ATF / System Manager / OEI) and the device trees. This extension
installs the kernel modules and firmware for the hardware on the SOM and on the
Sonata carrier, and autoloads the WiFi driver.

## What it installs

Derived from `lsmod` on a booted board, intersected with the `kernel-module-*`
packages this target actually builds — 11 of the 29 modules Variscite's own
6.12 reference image loads are built in (`=y`) on Avocado's 6.18 kernel and have
no package, so a straight copy of the vendor's module list fails the install.

| group | modules |
| --- | --- |
| Carrier (Sonata) | `mcp251xfd` (SPI CAN-FD), `ads7846` (touch), `ov5640` (camera), `rtc-ds1307`, `snd-soc-wm8904` (codec) |
| Audio | `snd-soc-fsl-sai`, `snd-soc-fsl-utils`, `imx-pcm-dma` |
| Media | `mxc-jpeg-encdec`, `v4l2-jpeg` |
| CAN core | `flexcan`, `can-dev` |
| Crypto | `polyval-ce`, `polyval-generic`, `algif-hash` |
| WiFi / BT | `moal`, `mlan`, `btnxpuart`, `cfg80211`, `firmware-nxp-wifi-nxpiw612-sdio`, `wireless-regdb-static` |

The IW612 (Wi-Fi 6 + BT/802.15.4 over SDIO) is on the SOM. `moal` has no DT or
udev alias, so the SDIO card enumerates but the driver never binds unless it is
modprobed — hence the `modprobe:` entry. `btnxpuart` autoloads from its UART DT
node and is not listed there.

## SOM vs carrier

The DART-MX95 and the Sonata-Board are covered by one extension because
Variscite currently ships device trees for the Sonata carrier only
(`imx95-var-dart-sonata{,-m7,-neutron}.dtb`). If another carrier appears, the
carrier half belongs in its own extension declaring a
`/etc/avocado/devicetree.d/NN-<carrier>.conf` fragment with `DEVICETREE=` —
`avocado-devicetree-apply` reconciles those into the u-boot environment. The
machine keeps all three DTBs in the boot partition so that selection is a
runtime choice.

## SDK

`nativesdk-erofs-utils` is declared because `avocado ext image` shells out to
`mkfs.erofs`, which is in the target's SDK feed but not in the SDK bootstrap or
the SDK base image.
