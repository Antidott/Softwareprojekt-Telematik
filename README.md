# Remote Attestation and Secure Software Updates with MultiZone

Implementation of Remote Attestation and Secure Software Updates on top of MultiZone, based on previous work from a Bachelor Thesis.

Hardware: Xilinx Artix 7 35T Arty FPGA.

## Overview of this Repo

We forked the two relevant repo's from Hexfive. However, our project is based on work from our Uni intern Gitlab, and we did all of our coding there. The forks here are only relevant for testing the original multizone test application.

- `multizone-fpga` contains the **RISC-V soft-core implementation** for multiple boards, among them our arty board.
- `multizone-sdk` is the multizone firmware that has to be flashed on top of the RISC-V core, and an example application.

All documentation is in the `docs/` folder. It includes practical guides as well as background knowledge and descriptions of our work done.

File | Description
-|-
[getting-start_arch](./docs/getting-started_arch.md) | Getting started Guide for Arch Linux: Installation of the necessary tools, compiling the multizone projects.
[getting-start_win](./docs/getting-started_win.md) | Getting started Guide for Windows with VirtualBox, analog to the Arch Guide. Probably also useful for Ubuntu users as this is the used os in the VM.
[flashing-firmware](./docs/flashing-firmware.md) | Instructions for flashing the RISC-V core with Vivado and then loading the multizone firmware via OpenOCD.
[minicom](./docs/minicom.md) | More detailed instructions on how to use minicom as a serial terminal to the board.
[FPGA-Risc-V](./docs/FPGA-Risc-V.md) | Background knowledge on FPGAs and RISC-V.
[RA-on-Multizone_notes](./docs/RA-on-Multizone_notes.md) | Notes / Summary of the Bachelor Thesis that describes the implementation of Remote Attestation on top of Multizone. Our work is based on this paper and the related code.
[remote-attestation](./docs/remote-attestation.md) | Describes our work towards fully implementing the remote attestation as described in the above paper.
[secure-software-updates-explained](./docs/secure-software-updates-explained.md) | Describes our work towards implementing secure software updates used the above remote-attestation feature.
[simulation-renode](./docs/simulation-renode.md) | Some notes from when we tried to use Renode to simulate a supported board and load the multizone firmware.
[gitlab-repo](./docs/gitlab-repo.md) | Description of our Repo/ branch structure on the Uni-intern Gitlab, where all of our code resides.
