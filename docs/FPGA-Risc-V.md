# Text zur Presentation 
- **What is a FPGA**
- **What is Risc-V**
- **Tool and Frameworks that we use**
## FPGA
FPGA is an abbreviation for Field Programmable Gate Array and is a programmable integrated circuit (IC) used in digital electronics. Unlike other integrated circuits designed for specific applications, an FPGA can be programmed and configured by the user to perform specific logic circuits and functions. An FPGA consists of a matrix of programmable logic blocks and connections between these blocks. These logic blocks can be combined and arranged to create different logic circuits. The programming of the logic blocks can be solved differently depending on the FPGA. A distinction can be made between methods that allow the FPGA to be programmed several times and methods that allow only one programming. With the multiple programmable FPGAs, the configuration is stored in memory cells. By programming the connections, the logic circuits can be connected in different ways to create more complex functions/circuits, such as microprocessors. So it is also possible to map Risc-V in an FPGA.
## Risc-V
Risc-V is a command set architecture based on the design principle of the Reduced Instruction Set Computer (RISC). It is an open standard, which is subject to the liberal BSD license. This means that RISC-V is not patented and may be freely used. A command set architecture specifies the behavior of the processor for the software by containing instructions. RISC has a reduced number of instructions, unlike CISC.
## Tool and Frameworks
To flash the FPGA with bitstreams we use the software Vivado, because the bitstream format expected by the FPGA is not open source. Vivado converts the generated configuration file into the bitstream format and sends it to the FPGA. 
RISC V GNU Toolchain is used to compile the bootloader for the FPGA.
We use MultiZone Trusted Firmware to ensure security and zone separation for Risc-V.
We chose Github because it didn't matter to anyone, since Gitlab and Github are based on git. (Of course, people who are not internal to the FU can access github.) For me personally, the reason was that I can work with my iPad via VS Code Web with Github because it doesn't work with Gitlab.
We chose Discord because it requires good communication between the groups and the organizers, whether via video or chat. 
