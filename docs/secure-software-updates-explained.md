# Secure Software Updates explained

The current, mocked approach to realize software updates is implemented by giving `zone1` direct write access to the memory of `zone4`
and writing a binary directly into it. This, of course, poses a security risk . Therefore, we are implementing a mechanism that 
sends an update via a secure channel (MZ Messages) to `zone4`, which places it at the required location in memory.

We will discuss the most important concepts of _MultiZone_ before moving to the explanation of our solution.

## Message Passing in MZ 

By default, it implements a service/request pattern. This means that each Zone has a command handler, which executes 
commands based on the messages it receives. However, it's important to note that this is not a requirement - 
different approaches are possible. Zones don't have to send or receive messages at all. Despite this, 
messages remain the only channel for Zones to interact with each other, making them a crucial part of the system's 
communication structure.

Send message: `MZONE_SEND(zoneID, message)` with message beeing an char[16]
Receive message: `MZONE_RECV(zoneID, pointer)` 

## Interrupts in MZ

 There are different types of interrupts, including maskable and non-maskable interrupts, as well as 
 vectored and non-vectored interrupts. To handle these, you can place either a pointer to a software interrupt 
 handler or a vector table in the status register. Each entry in the vector table points to an Interrupt Service Routine (ISR) 
 or interrupt handler. It's also worth noting that messages can trigger a machine software interrupt to all zones, further 
 integrating the two concepts of message passing and interrupt handling."

More information on this topics [here](https://github.com/hex-five/multizone-sdk/blob/master/manual.pdf).

##  Solution

![update procedure](https://raw.githubusercontent.com/Antidott/Softwareprojekt-Telematik/main/docs/update_mechanism.png)

[Work in progress]
