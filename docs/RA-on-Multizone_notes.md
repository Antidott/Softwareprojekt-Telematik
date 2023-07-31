# Notes - Remote Attestation on RISC-V devices using HexFive MultiZone

Notes on Bachelor Thesis "Remote Attestation on RISC-V devices using HexFive MultiZone" by Anton Oehler

Idea: Propose Remote Attestation (RA) on top of MultiZone based on Sancus' RA implementation

- **Trusted Execution Environment (TEE)**: isolated, trusted environment for excecuting security critical code; potentially on a remote device
- **Remote Attestation**: enable a remote verifier to measure and evaluate the security state of a device
  - device as a whole and/ or communication channel may be untrusted

## Background

### Sancus

Security Architecture

- Terminology:
    - **Software Module (SM)**: Some code/ application that should run be run on a Node (e.g. some IoT application)
    - **Software Provider (SP)**: The party that provides the Software Module (e.g. a customer)
    - **Infrastructure Provider (IP)**: The party that provides the Node
- Features:
    - Hardware-only Trusted Compute Base (TCB)
    - Secure Linking: secure IPC between software Modules
    - "program counter-based memory access control”: Protected memory can only be accessed by correspoding module; enforced by Memory Access Logic (MAL) circuits
- Remote attestation in Sancus:
    - Each Node has a node master key: K_N
    - Each SP has a key derived from the node master key: K_(N,SP)
    - Each SM has a unique identity ( Hash of .text section + start- & end-address of text and data section)
    - Each SM has a key derived from the SP's key and SM identity: K_(N,SP,SM) 
        - Changes when the module's code changes
        - Resides in hardware only; not accessible from software
    - Sancus adds 2 new Processor instructions to encrypt & decrypt with the SM key
    - Remote attestation: Request: Nonce, Response: MAC(Nonce)

### Multizone

Provides Trusted Execution Environment for RISCV (among other features)
  - "hardware-enforced software-defined separation of multiple functional areas within the same chip"
- Requirements:
    - "M" and "U" privilege modes (vs other TEE implementations that also require the "S" mode, which is usually only present in larger systems)
    - RISC-V Physical Memory Protection (PMP)
- Multizone TEE (=Multizone "nanoKernel") runs in M mode, uses PMP to protect memory
- User code is implemented in Zones that run in U mode (-> single privilege mode); `multizone.fpg` file defines:
    - Zone policy
    - Memory regions for each zone
    - Access permissions for these regions (r,w,e)
    - List of interrupts a zone receives    
    - Global option for max duration a zone can be executed at once

## Remote Attestation on Multizone based on RA in Sancus

- Map Sancus SM 1:1 to MultiZone zones
- Similar communication between modules possible in Multzone
- New _Attestation Zone_: attestation service
    - implements crypto primitives for attestation
    - creates & manages keys
    - has read-only access to memory regions that should be attested
- Zone/SM identity: hash of .text section & zone number
- Node master key: Stored in attestation zone; preloaded on deployment of node
- SP key & SM key derived like in Sancus; SP key known to SP
- Attestation zone (AZ) computes checksum of .text section of the modules that should be attested
- Remote attestion challenge:
    - SP generates nonce N0 and sends it to AZ
    - AZ derives SM key for to-be-attested zone
    - AZ computes MAC(N0) and sends it back to SP
    - SP compares with expected MAC

### Proof-of-Concept implementation

- Uses SiFive HiFive1 Rev B board
- 3 Zones:
    - Zone 1: communication zone: manages I/O over serial interface:
        - has rw access to shared memory buffer used by Zone 3
          -> insecure; only for demo purposes
    - Zone 2: attestation zone
    - Zone 3: software module, i.e. demo application
- Remote Software Update Demo
    - Update the software to proof that the MAC changes
    - Software updates are stored in RAM
    - Because of limited memory no complete zone binary could be loaded, but instead only a exemplary function
- Cryptography is implemented in software (hardware impl would be preferrable for real-world application -> potential future work?)
    - SpongeWrap AEAD implementation by Sancus team

