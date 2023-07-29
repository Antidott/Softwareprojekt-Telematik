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

It was important for us that the update procedure is capable of replacing any application in the corresponding zone, regardless of whether the service/request pattern is implemented or, for example, an endlessly running application with which no classic message exchange is possible. Therefore, we decided to process the messages via interrupts.

For this, there is an interrupt handler that is triggered as soon as a new message arrives.

The process is as follows:

1. Zone1 receives 'update_bin' command via uart
1.2. Z1 sets internal status that controls the behavior of the UART-ISR
2. SP sends binary
3. Z1 loops until all chunks of the update have been received and written into Z1's local buffer
4. Zone4 is informed about the upcoming update
5. Z4 notifies Z1 that the update can be received
6. Z1 sends the update in a loop until Z4 has received everything
7. Z4 writes the update into the "shared_buffer"
8. Z4 sets the program counter to the start of the "shared_buffer"

## Evaluation / Test Results
The current implementation is not functional at the moment and requires further fine-tuning. The reception of messages via interrupts is possible, as is the manual setting of program counters. However, the problem is receiving all chunks. While the first message from Z1 to Z4 is correctly received by the ISR, the subsequent messages go nowhere. Here, one could consider a simplified experimental setup that assumes that the update fits into exactly one message, or one removes the re-notification ('get_update') and starts transmitting the update immediately after the 'update_available' notification.
