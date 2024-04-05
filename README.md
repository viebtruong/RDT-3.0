# RDT 3.0 - Reliable Data Transfer Protocol

## Overview

RDT 3.0 is an implementation of the Reliable Data Transfer protocol, meticulously crafted as part of the coursework for COMPSCI 453 (Computer Networks) at the University of Massachusetts Amherst. This project aims to ensure accurate and reliable transmission of data over a potentially unreliable network layer, incorporating sophisticated features such as error detection, acknowledgments, and retransmissions to address packet corruption, loss, and ordering.

## Features

- **Error Detection**: Utilizes checksums to detect errors in data packets.
- **Positive Acknowledgment with Retransmission (PAR)**: Ensures that data is retransmitted until it is correctly received and acknowledged by the receiver.
- **Sequence Numbers**: Differentiates between new data and retransmissions, preventing duplicate data delivery.
- **Timeouts**: Detects lost packets and triggers retransmission.

## Getting Started

### Installation

1. Clone the repository to your local machine:
2. Navigate to the project directory:

### Running the Protocol

To start a simple file transfer using RDT 3.0, run the sender and receiver applications in separate terminal windows.
For more information, please refer to the PA.pdf

