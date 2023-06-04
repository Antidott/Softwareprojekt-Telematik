# Getting Started - Arch Linux

## Install Vivado

Vivado is supported in the Arch User Registry, see instructions on the [Arch Wiki](https://wiki.archlinux.org/title/Xilinx_Vivado).
Note that you need to download the Vivado ML Edition as _Xilinx Unified Installer <year>.<version> SFD_ tarbell and place it in the same directory as the vivado aur package `PKGBUILD`. (In the Arch wiki they instruct to download "Vivado HLx <year>.<version>: All OS installer Single-File Download". However I wasn't able to find a Vivado **HL** and for me it worked with the **ML** as well).
It will install vivado in the path `<vivado-aur-package>/pkg/vivado/opt/Xilinx/Vivado/<year>.<version>/`

### Install Cable Drivers

The installation does not include cable drivers, which are needed to autodetect and flash a connected board.
To manually install the cable drivers execute ([source](https://digilent.com/reference/programmable-logic/guides/install-cable-drivers)):

```sh
# cd <vivado_install_path>/data/xicom/cable_drivers/lin64/install_script/install_drivers/ 
# ./install_drivers
```

### Install Board FIles

The installation also does not include necessary board files. They have to be manually downloaded and extract into `<vivado_install_path>/data/boards/board_files`, as instructed [here](https://digilent.com/reference/programmable-logic/guides/installing-vivado-and-vitis#install_digilent_s_board_files).

## Install RISC-V Toolchain

From the [multizone-fpga/README](https://github.com/hex-five/multizone-fpga#risc-v-toolchain):

```sh
## From within the directory in which the RISCV toolchain should be installed
wget https://hex-five.com/wp-content/uploads/riscv-gnu-toolchain-20210618.tar.xz
tar -xvf riscv-gnu-toolchain-20210618.tar.xz
## Set env variable 
export RISCV=$PWD/riscv-gnu-toolchain-20210618
```

## Install OpenOCD on chip debugger

From the [multizone-sdk/README](https://github.com/hex-five/multizone-sdk):

```sh
## From within the directory in which openOCD should be installed
wget https://hex-five.com/wp-content/uploads/riscv-openocd-20210807.tar.gz
tar -xvf riscv-openocd-20210807.tar.gz
## Set env variable 
export OPENOCD=$PWD/riscv-openocd-20210807
```

## Other prerequisites

### JDK

Building the bitstream requires open-jdk to be installed.
The implementation unfortunately uses the java `SecurityManager`, which has been deprecated since Java 17.
A short-term workaround is to install an older Java version, e.g. `jdk11-openjdk`, and temporarily switch to using this version with the `archlinux-java` script:

```sh
# archlinux-java set java-11-openjdk
```

### Others

- Device Tree Compiler ([dtc](https://archlinux.org/packages/community/x86_64/dtc/))

## Building the hexfive/multizone-fpga bitstream

**Instructions from the repo README**:

```sh
git clone https://github.com/hex-five/multizone-fpga.git
cd multizone-fpga
git submodule update --init --recursive --jobs 8
export PATH=$PATH:<vivado_install_path>/bin
```

Some of the git submodules are referenced by `git@...` urls, which sometimes can cause issues. If that's the case, configure git to replace the url with an `http` address: `git config --global --unset url."https://".insteadOf git://`. Note that unfortunately for some modules no `https` address exists, so you might need to first execute the `git submodule update` without the mentioned git config and then again with it.

Before building the mcs files, one more patch is needed for romgen targe, as described in [this issue](https://github.com/hex-five/multizone-fpga/issues/9):
(_TODO: directly patch our submodule in this repo_)

```diff
diff --git a/rocket-chip/scripts/vlsi_rom_gen b/rocket-chip/scripts/vlsi_rom_gen
index 3e97070db..c0185d1d2 100755
--- a/rocket-chip/scripts/vlsi_rom_gen
+++ b/rocket-chip/scripts/vlsi_rom_gen
@@ -94,7 +94,7 @@ def iterate_by_n(it, n):
                         'Iterable length not evenly divisible by {}'.format(n)
                     )
                 else:
-                    raise
+                    return
         yield batch
```

Finally, build the bitstream:

```sh
make -f Makefile.x300arty35devkit mcs
```

_Note: I had the same issue as <https://github.com/sifive/freedom/issues/96>, where the `BootRom` was not found. As also described in that issue, I was able to solve it by simple removing the `multizone-fpga` folder and re-cloning with the `submodule update --init --recursive` command. I don't know what the actual issue behind this is._

## Building the hexfive/multizone-sdk example application

The instructions are based on [multizone-sdk/README](https://github.com/hex-five/multizone-sdk/blob/147d3f0eae539b767c2272efd67b220ba8ef6b67/README.md)

### Arch Prerequisites

- Java (see above, although here the version does not matter)
- Arch packages:

```sh
pacman -S --needed make hidapi libftdi
```

- AUR package: `ncurses5-compat-libs`

### Linux USB udev rules

Create/ edit `/etc/udev/rules.d/99-openocd.rules`

```sh
# Future Technology Devices International, Ltd FT2232C Dual USB-UART/FIFO IC
SUBSYSTEM=="tty", ATTRS{idVendor}=="0403",ATTRS{idProduct}=="6010", MODE="664", GROUP="plugdev"
SUBSYSTEM=="usb", ATTR{idVendor} =="0403",ATTR{idProduct} =="6010", MODE="664", GROUP="plugdev"

# Future Technology Devices International, Ltd FT232 USB-Serial (UART) IC
SUBSYSTEM=="tty", ATTRS{idVendor}=="0403",ATTRS{idProduct}=="6001", MODE="664", GROUP="plugdev"
SUBSYSTEM=="usb", ATTR{idVendor} =="0403",ATTR{idProduct} =="6001", MODE="664", GROUP="plugdev"

# Olimex Ltd. ARM-USB-TINY-H JTAG interface
SUBSYSTEM=="tty", ATTRS{idVendor}=="15ba",ATTRS{idProduct}=="002a", MODE="664", GROUP="plugdev"
SUBSYSTEM=="usb", ATTR{idVendor} =="15ba",ATTR{idProduct} =="002a", MODE="664", GROUP="plugdev"

# SiFive HiFive1 Rev B00 - SEGGER
SUBSYSTEM=="tty", ATTRS{idVendor}=="1366",ATTRS{idProduct}=="1051", MODE="664", GROUP="plugdev
```

In order order to enable access to serial and debug interface without root permissions, add the current user to the `plugdev` and `dialout` groups (see Freedom Studio User Manual 2020-11, page 165 - Enable Access to USB Devices).
