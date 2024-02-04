#!/usr/bin/env python3
# Last updated: Oct, 2021

import sys
import socket
import datetime 
from checksum import checksum, checksum_verifier

CONNECTION_TIMEOUT = 6000 # timeout when the receiver cannot find the receiver within 60 seconds
FIRST_NAME = "VIET"
LAST_NAME = "TRUONG"

def start_receiver(server_ip, server_port, connection_ID, loss_rate=0.0, corrupt_rate=0.0, max_delay=0.0):
    """
     This function runs the receiver, connnect to the server, and receiver file from the receiver.
     The function will print the checksum of the received file at the end. 
     The checksum is expected to be the same as the checksum that the receiver prints at the end.

     Input: 
        server_ip - IP of the server (String)
        server_port - Port to connect on the server (int)
        connection_ID - your receiver and receiver should specify the same connection ID (String)
        loss_rate - the probabilities that a message will be lost (float - default is 0, the value should be between 0 to 1)
        corrupt_rate - the probabilities that a message will be corrupted (float - default is 0, the value should be between 0 to 1)
        max_delay - maximum delay for your packet at the server (int - default is 0, the value should be between 0 to 5)

     Output: 
        checksum_val - the checksum value of the file sent (String that always has 5 digits)
    """

    print("Student name: {} {}".format(FIRST_NAME, LAST_NAME))
    print("Start running receiver: {}".format(datetime.datetime.now()))

    checksum_val = "00000"
    total_packet_sent = 0
    total_packet_recv = 0
    total_corrupted_pkt_recv = 0

    ##### START YOUR IMPLEMENTATION HERE #####

    receiver = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    #Connect to gaia.cs.umass.edu
    receiver.connect((server_ip,server_port))

    #Format the message
    message = f"HELLO R {loss_rate} {corrupt_rate} {max_delay} {connection_ID}"
    #Send message
    receiver.sendall(message.encode())
    #Receive the response from server
    response = receiver.recv(1024).decode()
    # check response
    while True:
        if 'WAITING' in response:
            print("Receiver is waiting...")
            # Wait for another response from the receiver
            response = receiver.recv(1024).decode()
            
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
            receiver.close()
        else:
            print("Unexpected response from the server.")
            receiver.close()    
    
    #Make a packet by required format
    #We only send the packet with ACK number to sender
    def make_pkt(ack_num):
        pkt = f"  {ack_num}                      "
        pkt += str(checksum(pkt))
        return pkt
    #Send packet to receiver
    def udt_send(sndpkt):
        receiver.sendall(sndpkt.encode())
    #Check if the receive packet is corrupt
    def corrupt(rcvpkt):
        if checksum_verifier(rcvpkt):
            return False
        return True    
    data = ""

    seq_num = [0, 1]
    ack_num = [0, 1]
    notFinish = True
    #While still receiving packet from sender
    while notFinish:
        while True:

            # Wait for 0 from below
                #Receiving packet
                rdt_rcv = receiver.recv(30).decode("utf-8")
                
                #Check if the packet is the last packet
                if rdt_rcv == "":
                    notFinish = False
                    break
            
                #total_packet_recv += 1

                #Check if the received packet is corrupted
                if corrupt(rdt_rcv):
                    #total_corrupted_pkt_recv += 1
                    
                    #Send ACK 1 to sender
                    packet = make_pkt(ack_num[1])
                    udt_send(packet)
                    
                    #total_packet_sent += 1
                #If packet corrupt, send negative ack to sender and continue wait for next packet    
                    continue
                                    
                #If the sequence number is not correct
                if seq_num[0] != int(rdt_rcv.split()[0]):

                    #Send ACK 1 to sender
                    packet = make_pkt(ack_num[1])
                    udt_send(packet)
                    
                    #total_packet_sent += 1
                #If received packet has incorrect seq_num, send negative ack to sender and continue waiting for next packet
                    continue
                

                #If the packet received packet is not corrupt and has the correct sequence number. 
                # Extract the packet and deliver
                data += rdt_rcv[4:24]
                
                #make packet and send back to sender with ack 0
                packet = make_pkt(ack_num[0])
                udt_send(packet)
            
                # increment total packet sent
                #total_packet_sent += 1
                break
            
        if not notFinish:
            continue
        while True:
            # Wait for 1 from below
                #Receiving packet
                rdt_rcv = receiver.recv(30).decode("utf-8")
                
                #If the packet is the last one, then stop
                if rdt_rcv == "":
                    notFinish = False
                    break
                
                #total_packet_recv += 1

                #If the packet is corrupted
                if corrupt(rdt_rcv):
                    #total_corrupted_pkt_recv += 1
                    
                    #Make packet and send with ACK 0
                    packet = make_pkt(ack_num[0])
                    udt_send(packet)
                    
                    #total_packet_sent += 1
                    #If packet corrupt, send negative ack to sender and continue wait for next packet    
                    continue
                                    
                #If the packet received has correct sequence number
                if seq_num[1] != int(rdt_rcv.split()[0]):
                    #Make packet and send with ACK 0
                    packet = make_pkt(ack_num[0])
                    udt_send(packet)
                    
                    total_packet_sent += 1
                #If received packet has incorrect seq_num, send negative ack to sender and continue waiting for next packet

                    continue
                
                #If packet is not corrupted and has correct sequence number
                #Extract the packet and deliver
                data += rdt_rcv[4:24]
                
                #Make packet and send it with ACK 1
                packet = make_pkt(ack_num[1])
                udt_send(packet)
                #total_packet_sent += 1
                break

        
    
    checksum_val = checksum(data)
    receiver.close() 
    ##### END YOUR IMPLEMENTATION HERE #####

    print("Finish running receiver: {}".format(datetime.datetime.now()))

    # PRINT STATISTICS
    # PLEASE DON'T ADD ANY ADDITIONAL PRINT() AFTER THIS LINE
    print("File checksum: {}".format(checksum_val))

    return checksum_val

 
if __name__ == '__main__':
    # CHECK INPUT ARGUMENTS
    if len(sys.argv) != 7:
        print("Expected \"python PA2_receiver.py <server_ip> <server_port> <connection_id> <loss_rate> <corrupt_rate> <max_delay>\"")
        exit()
    server_ip, server_port, connection_ID, loss_rate, corrupt_rate, max_delay = sys.argv[1:]
    # START RECEIVER
    start_receiver(server_ip, int(server_port), connection_ID, loss_rate, corrupt_rate, max_delay)
