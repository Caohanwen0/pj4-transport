# Reliable Transport Protocol

## High-level approach
I followed the advice given by the implementation strategy. 

## Features
1. For dropped packets: I use a straight-forward approach to send ACK messages and detect dropped packets. The sender will save the packets that it needs to send in sequence and send them. The receiver will send ACK back whenever it receives a packet, regardless of whether it is receiving in order. The sender will know a packet is dropped if it has been sent for a while and hasn't been acked yet. So it will simply resend the dropped packet before continue to send other unsent packets in sequence. This implementation has two benefits: first, it is easy to implement on both side, as the receiver doesn't need to keep track of the latest in-order sequence number of packets it receives, and neither does the sender; second, it reduces the chances of resending packets that have been successfully sent after the dropped packet. In our code, the receiver will simply cache all the valid received packets in sequence, and it will output the data of a packet only after all packets before it (using sequence number to ensure this) has been outputted.
2. To detect corrupted packets, I use the CRC32 hashing algorithm to compute a checksum and includes it in my data. The receiver can check the integrity of packets by decoding the data and compare its hash value with the checksum, which is also in the packet. This ensures that any tiny corruption in data can be detected.
3. When timeout happens in one packet, the congestion window is reduced to half its size instead of being dropped straight to 1. 
4. I set the initial slow-start threshhold to 15 instead of 1. Just like the strategy of 3., because there are only two device communicating in the testing network, it's not neccessary to set the slow-start initial value too low, since we can assert that the cases where network is so busy that slow-start stage will end very quickly is unlikely to happen. In real-life testing, this initial value proves to be an improvement in efficiency.


## The challenge I face
I spend a lot of time debugging at first because my code is failing at even the first test. I manage to resolve this by moving log function outside the object member function. I am still not sure why that is happening.


## How I Test my code
I use 
```
$ ./run <config-file>
```
to test my code's correctness and efficiency. I fine-tune the hyper parameters such as the initial slow-start threshhold, the alpha factor which is used to update our RTT estimate, etc, in order to achieve a higher performance.
