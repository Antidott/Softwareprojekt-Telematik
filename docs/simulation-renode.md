# Simulating the multizone-sdk in Renode

[Renode](https://github.com/renode/renode/) is a tool for simulating embedded systems.

Due to some hardware issues we did some work towards running the multizone-sdk firmware on a board in a simulation.
**This was not directly successful and we did not invest much time**. Eventually we just got the hardware working.
However, this documents describes what the idea was and the WIP (done on Linux).

Renode supports multiple platforms that can be simulated. Among them is the SiFive FE310 target (= the SiFive HiFive1 Rev B) that the multizone-sdk supports. The [the Bachelor Thesis](./RA-on-Multzone_notes.md) also targeted this board.

Prerequisite: the multizone-sdk firmware was compiled to the _multizone.hex_ file as described in the getting started guides.

We tried simulating the board and loading the firmware with the following _renode-fe310.resc_ script below (<PATH_TO_MULTIZONE_HEX> should be replaced with the actual path to the compiled _multizone.hex_.):

```resc
:name: SiFive-FE310
:description: This script runs the multizone-sdk sample on SiFive-FE310 platform.

$name?="SiFive-FE310"

using sysbus
mach create $name
# Load platform description that is already included in Renode repo
machine LoadPlatformDescription @platforms/cpus/sifive-fe310.repl

$hex?=@<PATH_TO_MULTIZONE_HEX>


# Create a pseudo-terminal to which we can connect from host device.
emulation CreateUartPtyTerminal "term" "/tmp/uart"
connector Connect sysbus.uart1 term

# Load the firmware to the board
sysbus LoadHEX $hex

```

Loading this script in Renode worked with:

```sh
renode <PATH_TO_SCRIPT>
```

However, when trying to connect with minicom serial terminal console (by changing the _Serial port setup_ to use '/tmp/uart' as _A - Serial Device_) we did not get the expected multizone-sdk output printed.
We did not try anything more.
