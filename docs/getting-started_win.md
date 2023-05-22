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
wget https://hex-five.com/wp-content/uploads/riscv-gnu-toolchain-20210618.tar.xz
tar -xvf riscv-gnu-toolchain-20210618.tar.xz
```

## Other prerequisites

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