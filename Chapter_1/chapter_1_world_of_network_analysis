# Define Network Analysis

**Network Analysis** - AKA, protocol analysis, is the process of listening to and analyzing network traffic.

## Follow an Analysis Example

Typical network analysis session includes several tasks:

- Capture packets at the appropriate location
- Apply filters to focus on traffic of interest
- Review and identify anomalies in the traffic. 

**Process of Navigating to www.wireshark.org and Downloading Wireshark**

You will see two DNS requests:

- One for IPv4 (A record)
- One for IPv6 (AAAA record)

Your client makes a TCP connection to www.wireshark.org and then sends an HTTP GET request asking for the default page (GET /)

<img src = "Chapter_1/chapter_1_figure_1.png">

You will see the HTTP server respond with a 200 OK response and the page download begins. 

When you click the **Download Wireshark** button, your system sends a request for **/download.html*

Your system may do a DNS query to find the IP address of the download server before making a new TCP connection to that IP address and finally sending a GET request for the Wireshark file:

<img src = "Chapter_1/chapter_1_figure_2.png">

## Walk-Through of a Troubleshooting Session

**Scenario**: This is based on an actual customer visit. Poor performance was reported when clients uploaded files to a server from a branch office to the corporate headquarters. The upload time went from 3 minutes to 10 - 15 minutes.

**Step 1: Plan:**

Identifying where to start - the packet capture was planned to be performed as close to the complaining user's (Mike) machine as possible. 

Connect wireshark to Mike's upstream switch and span Mike's port. 


**Step 2: Caputre:**

Traffic capturing without any filter in place was initiated. During the capture, Mike was asked to perform a file upload. 

Numerous packets with a black background and red foreground - Bad TCP packets. 

**Step 3: Analyze:**

In the trace file, we look at the TCP conversations, sorting on the highest byte count and filtering on this conversation.

We looked at the TCP connection to get a feel for round trip wire latency time - 65 ms. We also looked inside the TCP handshake packets to determine connection capabilities of Mike's machine and the server. 

We then created an IO Graph to see if there were sudden drops in IO rate or if the IO rate was bad all the way through, which is was with an average around 2.5 Mbps.

<img src = "Chapter_1/IO_Graph.png">

Next we examined the Expert Infos window. Over 12% of the traffic was marked as bad for some reason. Out of the 20,000 packets we captured during the test, there were over 1,000 Retransmissions and Fast Retransmissions. Hundreds of Duplicate ACKs indicate the receiver (the server) noticed much of the packet loss.

<img src = "Chapter_1/expert_info.png">

We did not see any `Previous Segment Not Captured` indications in the trace file. This indicates that Wireshark saw the original packet and the retransmission. Packet loss had not occurred yet.

When we looked at the retransmissions we noticed that Mike's machine was resending every packet from the lost packet forward - Not an expected recovery if the hosts were using `Selective Acknowledgements (SACK)`

<img src = "Chapter_1/SACK.png">

Mike's machine indicated it supported this feature, but the server did not. Significant packet loss will have a severe impact on performance without SACK in place.

Packet loss is the ultimate issue here. We need to address two questions:

- Where is packet loss occurring?
- Why didn't the server support SACK?

THe client was behaving properly by retransmitting data packets after the server asked for them (Duplicate ACKs). 

Since 99% of the time packet loss occurs at an interconnecting device, we knew we had to start capturing closer to the server.

**Step 4: Repeat**

The next step is to also set up Wireshark on the server side. 

A filter was applied for Mike's IP address as we asked Mike to repeat the upload process. 

We looked at the TCP handshake again and this time, Mike's handshake packet did not indicate his system supports SACK. Instead there was an illogical padding in the TCP header options area. This is a sign that a router likely stripped out some information and replaced the information with padding. Since the server believed that Mike couldn't support SACK, the server would not talk about it. The server was behaving properly in this case.

In order to understand which device could have altered the TCP handshake Options field, we captured trace files at different points along the path and eventually found a security device along the path that was removing this option from the handshake.

It was determined that the vendor who supplied this device indicated a bug with the device's software.

## Walk-Through of a Typical Security Scenario (aka Network Forensics)

**Scenario** A user noticed their system was acting strangely - from slow performance to an inability to shut down the machine or place it into hibernate mode.

One IT staffer, Colton, was focused on capturing the traffic to determine the cause of this behavior.

**Step 1: Plan**

The initial capture began close to the complaining host, SUSPECT1. Colton had baselines of normal activity and knew the protocls SUSPECT1 typically used.

Wireshark was not installed on SUSPECT1 in case the machine was infected with something. Colton connected a full-duplex tap to SUSPECT1 and connected Wireshark to the tap. Colton set up Wireshark in stealth mode.

**Step 2: Capture**

Colton began capturing all traffic without any filter in place. 

Coloton began to see a huge number of TCP SYN packets to TCP port 135 (NetBIOS Session Service) and port 445 (NetBIOS Directory Service).

**Step 3: Analyze**

In the traffic, Colton saw SUSPECT1 connect to an outside server on an unusual port - TCP port 18067. 

In the TCP stream, Colton noticed some recognizable commands - USeR, NiCK and JOiN, which are used in Internet Relay Chat (IRC) communications although they were not using all caps likely to avoid case-sensitive IDS/firewall detection rules. 

The IRC channel was being used to download a malicious application, which indicates that SUSPECT1 was infected with something. 

The host name of the IRC server was also discovered.

Using all the evidence available - host name, downloaded file name, port number in use, etc.., Colton learned he was up against a bot.

**Step 4: Secure**

Colton isolated the host from the network and began to clean it. Colton also began watching all network traffic to see which other hosts might be infected. 

By cutting off access to the IRC server, Colton mitigated any further damage.

### Troubleshooting Tasks for the Network Analyst

Troubleshooting tasks that can be performed with Wireshark include, but are not limited to:

- Locate faulty network devices
- Identify device or software misconfiguration
- Measure high delays along a path
- Locate the point of packet loss
- Identify network errors and service refusals
- Graph queuing delays

### Security Tasks for the Network Analyst

Security tasks performed with Wireshark include:

- Perform intrusion detection
- Identify and define malicious traffic signatures
- Passively discover hosts, operating systems and service
- Log traffic for forensics examination
- Capture traffic as evidence
- Test firewall blocking
- Validate secure login and data traversal

### Optimization Tasks for the Network Analyst

- Analyze current bandwidth usage
- Evaluate efficient use of packet sizes in data transfer applications
- Evaluate response times across a network
- Validate proper system configurations

### Application Analysis Tasks for the Network Analyst

- Analyze application bandwidth requirements
- Identify application protocols and ports in use
- Validate secure application data traversal

## Understand Security Issues Related to Network Analysis

Network analysis can be used to improve network performance and security, but can also be used for malicious tasks. For example, listening in on unecrypted communications on a public wireless network (Starbucks).

### Define Policies Regarding Network Analysis

- Who can use a network analyzer on the network and how, as well as when and where the network analyzer may be used.
- If you're a consultant performing network analysis services for a customer, consider adding a "Network Analysis" clause to your NDA. Define network analysis tasks and be completely forthcoming about the types of traffic that network analyzers can capture and view.

### Files Containing Network Traffic Should be Secured

Ensure the storage solution for the captured traffic is secured.

### Protect Your Network against Unwanted "Sniffers"

The best protection mechanism against network sniffing is to encrypt network traffic. 

However, encryption solutions will not protected the general network traffic that is broadcast into the network for device and/or service discovery. 

### Overcome the "Needle in the Haystack Issue"

- Place the analyzer appropriately
- Apply capture filters to reduce the number of packets captured.
- Apply display filters to focus on specific conversations, connections, protocls or applications.
- Colorize the conversations in more complex multi-connection communications.
- Reassemble streams for a clear view of data exchanged.
- Save subsets of the captured traffic into separate files.
- Build graphs depicting overall traffic patterns or apply filters to graphs to focus on particular traffic types.

<img src = "Chapter_1/wireshark_export.png">

## Review a Checklist of Analysis Tasks

**Proactive methods** 

Baselining network communications to learn the current status of the network and application performance. Proactive analysis can also be used to spot network problems before they are felt by the network users. 

For example, identifying the cause of packet loss before it becomes excessive and affects network communications helps avoid problems before they are even noticed.

**Reactive Analysis**

These techniques are employed after a complaint about network performance has been reported or when entwork issues are suspected.

**Analysis Tasks that can be performed using Wireshark**

- Find the top talkers on the network
- Identify the protocols and applications in use
- Determine the average packets per second rate and bytes per second rate of an application or all network traffic on a link.
- List all hosts communicating
- Learn the packet lengths used by a data transfer application
- Recognize the most common connection problems
- Spot delays between client requests due to slow processing
- Locate misconfigured hosts
- Detect network or host congestion that is slowing down file transfers
- Identify asynchronous traffic prioritization
- Graph HTTP flows to examine website referral rates
- Identify unusual scanning traffic on the network.
- Quickly identify HTTP error responses indicating client and server problems
- Quickly identify VoIP error responses indicating client, server or global errors
- Build graphs to compare traffic behavior
- Graph application throughput and compare to overall link traffic seen
- Identify applications that do not encrypt traffic
- Play back VoIP conversations to hear the effects of various network problems on network traffic
- Perform passive operating system and applicaion use detection
- Spot unusual protocols and unrecognized port number usage on the network
- Identify average and unacceptable service reponse times (SRT)
- Graph intervals of periodic packet generation applications or protocols

## Understand Network Traffic Flows

### Switching Overview

<img src="Chapter_1/switch_mechanism.png">

Switches forward packets based on the destination MAC address contained in the MAC header. Switches do not change the MAC or IP addresses in packets.

**Invalid Checksum**

When the packet arrives at a switch, the switch checks the packet to ensure it has the correct checksum and if it's incorrect, the packet is considered "bad" and discarded.

Switches should maintain error counters to indicate how many packets they have discarded because of bad checksums.

**Valid Checksum**

If the checksum is good, the switch examines the destination MAC of the packet and consults its MAC address table to determine if it knows which switch port leads to the host using that MAC address. If the switch does not have the target MAC in its tables, it will forward the packet out all switch ports in hopes of discovering the target. 


### Routing Overview

Routers forward packets based on the destination IP address in the IP header. 

**Invalid Checksum**

When a router receives a packet, it examines the checksum to ensure the packet is valid. If it's not, then the packet is dropped. 

**Valid Checksum**

If the checksum is valid, the router strips off the MAC header and examines the IP header to identify the **age** (in Time to Live) and destination of the packet. 

- If the packet is too old (Time to Live value of 1), the router discards the packets and sends an ICMP Time to Live Exceeded message back to the sender.

- If the packet is not too old, the router consults its routing tables to determine if the destination IP network is known. If the router is directly connected to the target network, it can send the packet to the target. The router decrements the IP header Time to Live value and then creates and applies a new MAC header to the packet before forwarding it.

<img src = "Chapter_1/routing_mechanism.png">

### Proxy, Firewall and NAT/PAT Overview

Firewalls examine the traffic and allow/disallow communications based on a set of rules. For example, blocking all TCP connection attempts from hosts outside the firewall that are destined to port 21 on internal servers. 

Basic firewalls operate at layer 3 to forward traffic. The firewall prepends a new MAC header on the packet before forwarding it. Additional packet alteration will take place if the firewall supports added features, such as NAT or proxy capabilities.

NAT systems alter the IP addresses in the packet - hiding the client's private IP address. A basic NAT system simply alters the source and destination IP address of the packet and tracks the connection relationships in a table to forward traffic properly when a reply is received. 

Port Address Translation (PAT) systems also alter the port information and use this as a method for demultiplexing multiple internal connections when using a single outbound address. 

Proxy servers - the client connects to the proxy server and the proxy server makes a separate connection to the target. 

<img src = "Chapter_1/firewall.png">

### Other Technologies that Affect Packets

**Virtual LAN (VLAN) tagging (defined as 802.1Q)** adds an identification (tag) to the packets. THis tag is used to create virtual networks in a switched environment. 

**Multiprotocol Label Switching (MPLS)** is a method of creating virtual links between remote hosts. MPLS packets are prefaced with a special header by MPLS edge devices. For example, a packet sent from a client reaches an MPLS router where the MPLS label is placed on the packet. The packet is now forwarded based on the MPLS label, not routing table lookups.

## Launch an Analysis Session

### Step 1 Install Wireshark

www.wireshark.org/docs/wsug_html_chunked/ChIntroPlatforms.html

### Step 2 Launch Wireshark

Click on your wired network adapter listed in the Interface List ont he Start Page and click start. Wireshark should be capturing traffic now. (If your adapter is not listed, you cannot capture traffic. Visit wiki.wireshark.org/CaptureSetup/NetworkInterfaces for assistance.)

### Step 3 Clear DNS cache and Navigate to a URL

Navigate to www.chappelU.com

### Step 4 Stop Capture

Select `Capture | Stop` on the Main Menu or click the `Stop Capture` button on the main toolbar.

### Step 5 Analyze the captured traffic

You should see a DNS query. If your system supports both IPv4 and IPv5 you may see two DNS queries: one for the IPv4 address (A record) of wwww.chappelU.com and one for the IPv6 address (AAAA record) of www.chappelU.com


<img src = "Chapter_1/capture_lab">


### Step 6 Save the Capture

Select `File | Save` and create a \mytraces directory. Save your file using the name chappelu.pcapng

