# pfSense `if_enic` Kernel Module

Binary package distribution of the Cisco VIC Ethernet kernel module for **pfSense CE 2.7.2** on **amd64**.

## Package

| Property | Value |
|---|---|
| Package | `pfsense-if-enic-2.7.2.1400094.pkg` |
| Version | `2.7.2.1400094` |
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

The package enforces this itself: a pre-install checks that `kern.osreldate` equals `1400094` and refuses to install on any other kernel build, before any file is written.

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

The package configures boot-time loading by adding one entry to `/boot/loader.conf.local`:

```text
if_enic_load="YES"
```

The package never modifies `/boot/loader.conf`. During installation the package refreshes `/boot/modules` linker hints and attempts to load the module immediately. On removal it unloads the module and deletes that exact entry, removing `/boot/loader.conf.local` only when no other entries remain. Everything else in `/boot/loader.conf.local` is preserved.

## Installation

Copy the package to the pfSense system, then install it as `root`:

```sh
pkg add /path/to/pfsense-if-enic-2.7.2.1400094.pkg
```

The package installs the module, refreshes the `/boot/modules` linker hints, adds the boot-time loader entry, and attempts to load the module immediately. Verify:

```sh
kldstat | grep if_enic
```

If the module is not loaded (for example on a kernel version outside this package's target), load it manually:

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

## Boot-time loading

Boot-time loading is enabled by the package itself during installation: it adds one line to `/boot/loader.conf.local`:

```text
if_enic_load="YES"
```

Do not edit `/boot/loader.conf` directly; pfSense regenerates that file. To disable automatic loading, remove the line above from `/boot/loader.conf.local`. Keep a console or recovery path available when adjusting boot-time loading.

## Removal

Remove the package:

```sh
pkg delete pfsense-if-enic
```

Removal unloads the module if it is loaded, deletes the `if_enic_load` line from `/boot/loader.conf.local`, and refreshes the linker hints; no separate `kldunload` is required.

If an `enic` interface is active, stop using or remove the interface assignment before removing the package.

## Integrity

SHA-256 checksum of `pfsense-if-enic-2.7.2.1400094.pkg`:

```text
aec667d8c4170e1c79d9c7647bb603891da0ab02e3b8bc6ac439fcb7e32b7c08
```

Verify the downloaded file before installation:

```sh
sha256 -q pfsense-if-enic-2.7.2.1400094.pkg
```

The module included in the package has SHA-256 checksum:

```text
1affea427eaa9cd178c5b029a05190329a27b69667e5d01016049b6f4788757b
```

## License and notices

This package contains the FreeBSD `enic` driver source implementation and is distributed as a binary package. Refer to the license and copyright notices in the corresponding [FreeBSD source tree](https://github.com/pfsense/FreeBSD-src) for applicable terms.

Cisco, Cisco VIC, and related product names are trademarks of Cisco Systems, Inc. This package is not affiliated with or endorsed by Cisco Systems, Inc.
