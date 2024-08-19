---
tags:
  - Internet
  - Networks
---
# Core principles
## Definition
A way to understand complex systems by simplifying them in layers. Each layer has its own function in the information delivery, and that layer in source speaks the same language as the same layer in destination.
## Interface
The communication between two different layers is called **interface**.
## Protocol
## RFC (Request For Comments)
The official documents that have the information about every [[#Protocol]] on the Internet. You can find the RFC you want in \[[[#References|1]]\]
# Example
![[Layer-architecture-example.png]]
A spanish person wants to send a letter to a japanese person. To this end:

- The letter is firstly translated from spanish to english by a translator layer
- The translated letter is packed and transmitted by a transmission layer
- The received letter is translated from english to japanese
- The letter finally reaches the destination

In this information exchange, there are different layers, each one with a certain purpose:
- The end-to-end layer, which consists of the origin spanish person and destination japanese person
- The translation layer, which consists of the spanish-english translator and english-japanese translator
- The transmission layer, whose purpose is to transmit the layer from one side to the other

Therefore, for example: the transmission layer only understands how to deliver the packet, but cannot understand its content, and that transmission layer must speak in the same language in source and destination to make the delivery possible.

# References
1. [» RFC Editor (rfc-editor.org)](https://www.rfc-editor.org/)