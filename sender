#!/usr/bin/env python3
# Last updated: Oct, 2021


import sys
import socket
import datetime
from checksum import checksum,checksum_verifier

CONNECTION_TIMEOUT = 6000 # timeout when the sender cannot find the receiver within 60 seconds
FIRST_NAME = "VIET"
LAST_NAME = "TRUONG"

def start_sender(server_ip, server_port, connection_ID, loss_rate=0, corrupt_rate=0, max_delay=0, transmission_timeout=60, filename="declaration.txt"):
    """
     This function runs the sender, connnect to the server, and send a file to the receiver.
     The function will print the checksum, number of packet sent/recv/corrupt recv/timeout at the end. 
     The checksum is expected to be the same as the checksum that the receiver prints at the end.

     Input: 
        server_ip - IP of the server (String)
        server_port - Port to connect on the server (int)
        connection_ID - your sender and receiver should specify the same connection ID (String)
        loss_rate - the probabilities that a message will be lost (float - default is 0, the value should be between 0 to 1)
        corrupt_rate - the probabilities that a message will be corrupted (float - default is 0, the value should be between 0 to 1)
        max_delay - maximum delay for your packet at the server (int - default is 0, the value should be between 0 to 5)
        tranmission_timeout - waiting time until the sender resends the packet again (int - default is 60 seconds and cannot be 0)
        filename - the path + filename to send (String)

     Output: 
        checksum_val - the checksum value of the file sent (String that always has 5 digits)
        total_packet_sent - the total number of packet sent (int)
        total_packet_recv - the total number of packet received, including corrupted (int)
        total_corrupted_pkt_recv - the total number of corrupted packet receieved (int)
        total_timeout - the total number of timeout (int)

    """

    print("Student name: {} {}".format(FIRST_NAME, LAST_NAME))
    print("Start running sender: {}".format(datetime.datetime.now()))

    checksum_val = "00000"
    total_packet_sent = 0
    total_packet_recv = 0
    total_corrupted_pkt_recv = 0
    total_timeout =  0

    print("Connecting to server: {}, {}, {}".format(server_ip, server_port, connection_ID))

    ##### START YOUR IMPLEMENTATION HERE #####

    sender = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    #Connect to gaia.cs.umass.edu
    sender.connect((server_ip,server_port))
    sender.settimeout(None)
    #Format the message
    message = f"HELLO S {loss_rate} {corrupt_rate} {max_delay} {connection_ID}"
    #Send message
    sender.sendall(message.encode())
    sender.settimeout(CONNECTION_TIMEOUT)
    #Receive the response from server
    response = sender.recv(1024).decode()
    # check response
    while True:
        if 'WAITING' in response:
            print("Sender is waiting...")
            # Wait for another response from the receiver
            response = sender.recv(1024).decode()
            
        if 'OK' in response:
            break

        elif 'ERROR' in response:
            #Possible errors
            if 'Incorrect Parameter Values' in response:
                print("Error: Incorrect parameter values provided.")
            elif 'CONNECTION ID' in response:
                print("Error: Connection ID is already in use.")
            elif 'NO MATCHING CONNECTION REQUEST' in response:
                print("Error: No matching connection request found in the last 60 seconds.")

            else:
                print("Error: Unknown error occurred.")
            sender.close()
        else:
            print("Unexpected response from the server.")
            sender.close()
    #Make a packet by required format
    def make_pkt(seq_num, ack_num, data):
        pkt = f"{seq_num} {ack_num} {data} "
        pkt += str(checksum(pkt))
        return pkt
    #Send packet to receiver
    def udt_send(sndpkt):
        sender.sendall(sndpkt.encode())
    #Check if the receive packet is corrupt
    def corrupt(rcvpkt):
        if checksum_verifier(rcvpkt):
            return False
        return True
    # read first 200 bytes
    with open(filename, 'r') as f:
        data = f.read(200)

    checksum_val =checksum(data)

    #Set timeout

    #Packet index to keep track of packet being sent
    pktsnd_idx = 0
    # Send the first 200 bytes of data
    while pktsnd_idx < len(data):
        while True:
            #Wait for call 0 from above
            seq_num = 0
            ack_num = 0
            #Wait for ACK0
            try:
                #20 bytes payload
                payload = data[pktsnd_idx:pktsnd_idx + 20]
                #Make a packet
                sndpkt = make_pkt(seq_num, ack_num, payload )

                #Check if the packet is the last packet
                if len(payload) == 0:
                    break


                #Padding the packet if the length of packet is < 30 bytes
                if len(sndpkt) < 30:
                    sndpkt = sndpkt.ljust(30)
                #Send packet
                udt_send(sndpkt)
                sender.settimeout(transmission_timeout)

                total_packet_sent += 1
                while True:
                    rdt_rcv = sender.recv(30).decode()
                        
                    #increment total packet recv
                    total_packet_recv += 1
                    
                    # check if ACK is corrupted
                    if corrupt(rdt_rcv):
                        total_corrupted_pkt_recv += 1
                        continue         
                    #If the received packet have negative ack

                    if int(rdt_rcv.split()[0])  != ack_num:
                        continue
                    # increment pktsnd_idx
                    pktsnd_idx += 20
                    break
                break
            #time out, resend the packet
            except socket.timeout:
                total_timeout += 1
                continue
        while True:    
            #Wait for call 1 from above
            seq_num = 1
            ack_num = 1
            try:
                    #20 bytes payload
                    payload = data[pktsnd_idx:pktsnd_idx+20]
                    #Make a packet
                    sndpkt = make_pkt(seq_num, ack_num, payload)
                    #Check if the packet is the final packet
                    if len(payload) == 0:
                        break

                    #Padding the packet if the length of packet is < 30 bytes
                    if len(sndpkt) < 30:
                        sndpkt = sndpkt.ljust(30)
                    #Send packet
                    udt_send(sndpkt)

                    total_packet_sent += 1
                    sender.settimeout(transmission_timeout)
 

                    #Wait for ACK1
                    while True:
                        rdt_rcv = sender.recv(30).decode()
                            
                        #Increment total packet recv
                        total_packet_recv += 1
                        
                        #Check if the received packet is corrupted
                        if corrupt(rdt_rcv):
                            total_corrupted_pkt_recv += 1
                            continue    #Wait for next message
                        #If the received packet have negative ack
                        if int(rdt_rcv.split()[0]) != ack_num:
                            continue    #Wait for next message
                        #If the received packet is not corrupt and correct ack_num
                    
                        #Increment pktsnd_idx
                        pktsnd_idx += 20

                        break
                    break
            #timeout, resend the packet 
            except socket.timeout:
                total_timeout += 1
                continue

    sender.close() 


    ##### END YOUR IMPLEMENTATION HERE #####

    print("Finish running sender: {}".format(datetime.datetime.now()))

    # PRINT STATISTICS
    # PLEASE DON'T ADD ANY ADDITIONAL PRINT() AFTER THIS LINE
    print("File checksum: {}".format(checksum_val))
    print("Total packet sent: {}".format(total_packet_sent))
    print("Total packet recv: {}".format(total_packet_recv))
    print("Total corrupted packet recv: {}".format(total_corrupted_pkt_recv))
    print("Total timeout: {}".format(total_timeout))

    return (checksum_val, total_packet_sent, total_packet_recv, total_corrupted_pkt_recv, total_timeout)
 
if __name__ == '__main__':
    # CHECK INPUT ARGUMENTS
    if len(sys.argv) != 9:
        print("Expected \"python3 PA2_sender.py <server_ip> <server_port> <connection_id> <loss_rate> <corrupt_rate> <max_delay> <transmission_timeout> <filename>\"")
        exit()

    # ASSIGN ARGUMENTS TO VARIABLES
    server_ip, server_port, connection_ID, loss_rate, corrupt_rate, max_delay, transmission_timeout, filename = sys.argv[1:]
    
    # RUN SENDER
    start_sender(server_ip, int(server_port), connection_ID, loss_rate, corrupt_rate, max_delay, float(transmission_timeout), filename)
