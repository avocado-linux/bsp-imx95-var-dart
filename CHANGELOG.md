# Changelog

## 0.1.0

Initial board support for the Variscite DART-MX95 on the Sonata-Board.

- Kernel modules for the SOM and the Sonata carrier, verified against a booted
  board and against the `kernel-module-*` packages the target actually builds.
- NXP IW612 WiFi/BT: `moal`/`mlan` plus `firmware-nxp-wifi-nxpiw612-sdio`, with
  `moal` autoloaded.
- `nativesdk-erofs-utils` for `avocado ext image`.
