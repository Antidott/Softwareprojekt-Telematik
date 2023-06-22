# Getting Started - Windows 10

## Install Linux Virtual Machine 
1. Download die ISO-Dile of Ubuntu 22.04.2 LTS from this [website](https://ubuntu.com/download/desktop)

2. Download Oracle VM VitualBox from this [website](https://www.virtualbox.org/wiki/Downloads). 

3. Download the Oracle VM VirtualBox Extension Pack from the same website.

4. Start Oracle VM VirtualBox and create a new VM with die ISO-FIle as Boot Media to install Ubuntu.

6. Install the updates from the update management.

5. Update System with the command ```sudo apt update```.

## Install Vivado

Download the BIN-File from this [website](https://www.xilinx.com/support/download.html) to install Vivado.
In order to install Vivado correctly run this commands:
```sh
sudo apt install libtinfo5  
sudo apt install libncurses5
```
Next set the BIN-File exe executable and run it with this command:
```sh
chmod +x <installer>.bin && sudo ./<installer>.bin
```
Then follow the installation program step by step.

### Install Board FIles

The installation also does not include necessary board files. The repository with that Board files have to be cloned from [Github](https://github.com/Xilinx/XilinxBoardStore) and copy into `<Vivado Install>\<Vivado_version>\data\xhub\boards\XilinxBoardStore\boards\`, as instructed [here](https://support.xilinx.com/s/article/The-board-file-location-with-the-latest-Vivado-tools?language=en_US).

## Install RISC-V Toolchain

From the [multizone-fpga/README](https://github.com/hex-five/multizone-fpga#risc-v-toolchain):

```sh
## From within the directory in which the RISCV toolchain should be installed
wget https://hex-five.com/wp-content/uploads/riscv-gnu-toolchain-20210618.tar.xz
tar -xvf riscv-gnu-toolchain-20210618.tar.xz
```

## Install OpenOCD on chip debugger

From the [multizone-sdk/README](https://github.com/hex-five/multizone-sdk):

```sh
## From within the directory in which openOCD should be installed
wget https://hex-five.com/wp-content/uploads/riscv-openocd-20210807.tar.gz
tar -xvf riscv-openocd-20210807.tar.gz
```

## Other prerequisites

### Linux prerequisites

```sh
sudo apt update
sudo apt install make gtkterm libhidapi-dev libftdi1-2
```

### Ubuntu 18.04 LTS additional dependency

**For biulding multizone-sdk**

```sh
sudo add-apt-repository "deb http://archive.ubuntu.com/ubuntu/ focal main universe"
sudo apt update
sudo apt install libncurses-dev
```

### JDK

Building the bitstream requires open-jdk to be installed.
The implementation unfortunately uses the java `SecurityManager`, which has been deprecated since Java 17.
A short-term workaround is to install an older Java version, e.g. `openjdk-11-jre`

```sh
sudo apt install openjdk-11-jre
```

### Device Tree Compiler
Building the bitstream requires Device Tree Compiler to be installed.

```sh
sudo apt-get install -y device-tree-compiler
```

### Python
Building the bitstream requires Python to be installed.

```sh
sudo apt install python-is-python3
```

### Linux USB udev rules

```sh
sudo vi /etc/udev/rules.d/99-openocd.rules

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
A reboot may be necessary for these changes to take effect.

## Building the hexfive/multizone-fpga bitstream

**Instructions from the repo README**:

```sh
git clone https://github.com/hex-five/multizone-fpga.git
cd multizone-fpga
git submodule update --init --recursive --jobs 8
export PATH=$PATH:<vivado_install_path>/bin
export RISCV=~/riscv-gnu-toolchain-20210618
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

## Building the hexfive/multizone-sdk example application

**Instructions from the repo README**:

```sh
cd ~
git clone https://github.com/hex-five/multizone-sdk.git
cd ~/multizone-sdk
export RISCV=~/riscv-gnu-toolchain-20210618
export OPENOCD=~/riscv-openocd-20210807
export BOARD=X300
make 
make load
```

