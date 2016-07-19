# Understanding networking

Computer networks are made up of the seven layers of the OSI model. Each layer solves a problem of the layer beneath it.

### Layer 1: The physical layer

This is a cable that connects two computers together. Now those 2 computers can send ones and zeroes to each other.

### Layer 2: The data link layer

L1 works great for connecting two computers but what if you want to put more than 2 computers in a network together? This is where L2 comes in. Every Network Interface Card has a MAC address. You connect a bunch of computers together and they broadcast *ethernet frames* to each other. Each frame has a destination MAC address and source MAC address. As machines receive ethernet frames, they check the destination MAC address and accept the frames if they're the destination. Frames destined for other MAC addresses are dropped. This is why network sniffing is possible.

L2 ethernet frames are then sent out on the L1 cable.

#### Hardware for L2

* HUB: This device just broadcasts everything everyone

* Switch or Bridge: This device is a little smarter than a hub. If a frame with MAC address X comes in from port A, it will remember that MAC X is located at port A. Next time a frame comes in with destination MAC X, it will no longer be broadcast and only sent out port A.

### Layer 3: The Network Layer

L2 can connect multiple machines but broadcasts everywhere. In larger networks (like the internet) you don't want to do that. L3 allows you to connect multiple L2 networks together by *routing* between them using IP addresses. Just like ethernet frames, *IP packets* have a source IP address and a destination IP address.

L3 *IP packets* are encapsulated in L2 *ethernet frames* and then sent out on the L1 cable.

When sending an IP packet, the network stack needs to know what MAC address to send it to. That's where the *Address Resolution Protocol* (ARP) comes in. When 10.0.0.1 sends a packets to 10.0.0.2, an ethernet frame (the ARP request) is broadcast to ask which MAC address has IP 10.0.0.2. When it arrives at the machine holding 10.0.0.2, it will return an ethernet frame (the ARP reply) stating its MAC Address. Once this is done, 10.0.0.1 and 10.0.0.2 are able to put their IP packets in ethernet frames and send them to each other's MAC addresses.

The OS will keep a cache of known MAC/IP combos. On Linux you can view this *arp cache* by typing `arp` in a terminal.

#### Why use IP at all if they're just being mapped to MAC addresses?

Because you can connect multiple L2 networks together through routing. Say we have network 192.168.0.0/24 and 172.16.0.0/24. This notation means IP addresses 192.168.0.1 to 192.168.0.255 are together in a subnet aka L2 broadcast domain. 172.16.0.1 to 172.16.0.255 are in a different subnet. It is impossible for machines in 192.168.0.0/24 to reach the MAC addresses of the machines in 172.16.0.0/24.

IP addresses allow us to put a router between these networks. They can allow 192.168.0.10 send an IP packet to 172.16.0.10 even though it's impossible for them to reach each other by MAC address.

![router](images/01_router.png)

Let's say machine A has 192.168.0.10 while machine B has 172.16.0.10. When A sends a packet to B, A will first notice that 172.16.0.10 is in another subnet. It knows it can't reach that machine's MAC address so it will not even try to ARP request it. Instead the OS will check its *routing table*. In the routing table it says that 172.16.0.0/24 can be reached through a router located at 192.168.0.1. Machine A will do an ARP request for 192.168.0.1 and send its IP packet over there. Next, the router will check its own routing table. It sees that it has an interface directly connected to 172.16.0.0/24. It will send out an ARP request for 172.16.0.10 on there which machine B will reply. Now it can send the IP packet to machine B.

This was an example of one hop. On a huge network like the internet, there will be multiple hops over a lot of routers until a packet arrives. You can see these hops by typing `traceroute google.com` in a terminal. You can see your OS' routing table by typing `ip route show`

### Layer 4: The Transport Layer

Understanding the layers above 3 in detail is not so important for these exercises. We'll go over them quickly.

When surfing to websites you may have seen a url like `http://192.168.0.10:80`. The http part is L7. We'll get to that. The :80 part is a TCP port and that's L4. The IP packets of L3 give us a way to get packets from point A to point B to in huge networks but it does nothing more. It does not allow multiple processes to listen on *ports* and does not provide any protection against data loss. On L4 there are 3 commonly used protocols.

* TCP: This protocol creates a connection between two IP Addresses and guarantees that data will arrive unchanged and in the right order. It has 65535 *ports* that processes can listen on. The above example of `http://192.168.0.10:80` makes a http call to TCP port `80` on IP address `192.168.0.10` TCP messages are referred to as *segments*

* UDP: Has 65535 ports just like TCP. Unlike TCP it does not offer any of the connection or data loss protection functionality. Also unlike TCP, it is blazing fast. UDP messages are referred to as *datagrams*.

* ICMP: Does not have ports nor connections. It is used for network devices to send error messages etc. to each other. The `ping` and `traceroute` command use ICMP.

L4 TCP segments, UDP datagrams and ICMP messages are encapsulated in L3 IP packets which are encapsulated in L2 ethernet frames which are sent out on the L1 cable.

### Layer 5: The Session layer

L5 handles connections between machines. It is not important to understand for these exercises.

### Layer 6: The Presentation layer

In L6 data is converted into the right formats. For example encryption would happen here.

### Layer 7: The Application layer

L7 is the application that software implements. For example *http*, *ssh*, *ftp*, *dns* are all L7.

## Exercises

This is the basic knowledge you'll need. Now off to the exercises. :p
