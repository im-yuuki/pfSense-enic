# pfSense `if_enic` Kernel Module

Binary package distribution of the Cisco VIC Ethernet kernel module for **pfSense CE 2.7.2** on **amd64**.

## Package

| Property | Value |
|---|---|
| Package | `pfsense-if-enic-2.7.2.1400094_1.pkg` |
| Version | `2.7.2.1400094_1` |
| Origin | `local/pfsense-if-enic` |
| Architecture | `FreeBSD:14:amd64` |
| pfSense release | `2.7.2-RELEASE` |
| Kernel release | `14.0-CURRENT` |
| Kernel ABI date | `1400094` |
| Package format | XZ-compressed FreeBSD package |

## Compatibility

This package is intended only for systems matching all of the following:

- pfSense CE `2.7.2-RELEASE`
- FreeBSD `14.0-CURRENT`
- `kern.osreldate=1400094`
- `amd64`

Do not install this package on pfSense 2.9.x, another pfSense release, a different FreeBSD release, or a system with a different kernel ABI date. Kernel modules are tightly coupled to the kernel ABI and configuration.

Driver source reference:

- Repository: [pfsense/FreeBSD-src](https://github.com/pfsense/FreeBSD-src)
- Branch: `RELENG_2_7_2`
- Revision: `8d2b56da39cec2f5241b41f35fd70b125ace1c0a`

## Package contents

The package installs:

```text
/boot/modules/if_enic.ko
/usr/local/share/doc/pfsense-if-enic/BUILD-INFO
```

The package does not automatically load the module and does not modify `/boot/loader.conf` or `/boot/loader.conf.local`.

## Installation

Copy the package to the pfSense system, then install it as `root`:

```sh
pkg add /path/to/pfsense-if-enic-2.7.2.1400094_1.pkg
```

Refresh the module linker hints and load the module manually:

```sh
kldxref /boot/modules
kldload if_enic
```

Confirm that the module is loaded:

```sh
kldstat -v | grep -A3 -B1 if_enic
```

The expected module registration includes:

```text
if_enic.ko (/boot/modules/if_enic.ko)
Contains modules:
        pci/enic
```

After loading, inspect the kernel log and network interfaces:

```sh
dmesg | grep -i enic
ifconfig -a
pciconf -lv
```

The driver will only create a network interface when a supported Cisco VIC Ethernet device is present and visible to the system. Loading the module successfully by itself does not confirm hardware attachment or link operation.

## Optional boot-time loading

After successful testing, the module can be configured to load at boot by adding the following line to `/boot/loader.conf.local`:

```text
if_enic_load="YES"
```

Do not edit `/boot/loader.conf` directly. Keep a console or recovery path available before enabling automatic loading.

## Removal

If the module is currently loaded, unload it first:

```sh
kldunload if_enic
```

Then remove the package:

```sh
pkg delete pfsense-if-enic
```

If an `enic` interface is active, stop using or remove the interface assignment before unloading the module.

## Integrity

SHA-256 checksum of `pfsense-if-enic-2.7.2.1400094_1.pkg`:

```text
18c5012c065dca92835aac56ea59b38acd7a3e9bb1a51a9924d95c7d8f033f9d
```

Verify the downloaded file before installation:

```sh
sha256 -q pfsense-if-enic-2.7.2.1400094_1.pkg
```

The module included in the package has SHA-256 checksum:

```text
1affea427eaa9cd178c5b029a05190329a27b69667e5d01016049b6f4788757b
```

## License and notices

This package contains the FreeBSD `enic` driver source implementation and is distributed as a binary package. Refer to the license and copyright notices in the corresponding [FreeBSD source tree](https://github.com/pfsense/FreeBSD-src) for applicable terms.

Cisco, Cisco VIC, and related product names are trademarks of Cisco Systems, Inc. This package is not affiliated with or endorsed by Cisco Systems, Inc.
