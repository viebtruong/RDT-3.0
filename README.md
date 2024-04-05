 RDT 3.0 - Reliable Data Transfer Protocol

Overview

RDT 3.0 is an implementation of the Reliable Data Transfer protocol, designed to ensure accurate and reliable transmission of data over a potentially unreliable network layer. This version incorporates features such as error detection, acknowledgments, and retransmissions to handle packet corruption, loss, and ordering.

 Features

- **Error Detection**: Utilizes checksums to detect errors in data packets.
- **Positive Acknowledgment with Retransmission (PAR)**: Ensures that data is retransmitted until it is correctly received and acknowledged by the receiver.
- **Sequence Numbers**: Differentiates between new data and retransmissions, preventing duplicate data delivery.
- **Timeouts**: Detects lost packets and triggers retransmission.

Getting Started

Prerequisites

- A Unix/Linux environment or equivalent command-line tools in other operating systems.
- Compiler or interpreter for the programming language used (Python).

Installation

1. Clone the repository to your local machine:
