<h1>Simulating and Blocking Adversaries with LimaCharlie</h1>

<h2>Description</h2>
This project consists of setting up 2 VMs, emulating an adversary, and blocking an attack.
<br />

<h2>Tools Used</h2>

- <b>LimaCharlie</b>
- <b>VMware Workstation</b>
- <b>Sysmon</b>
- <b>Sliver</b>

<h2>Environment</h2>

- <b>Ubuntu Server 22.04.1</b>
- <b>Windows 11 VMWare</b>

<h2>Project walk-through:</h2>

<h4>Detect a web page's IP address using a display filter</h4>
I am going to start a packet capture in Wireshark on my device's ethernet port. While Wireshark is capturing packets, I am going to open the target web page in my web browser (I am using google.com for this project).
<p align="center">
<img width="570" alt="Untitled" src="https://github.com/chau-eric/beginner-wireshark/assets/76719902/9254201c-9e74-4b0c-b6dd-ae903e7acfa7"><br/>
</p>
After stopping the packet capture, I used the display filter "tcp.port==443" to display only the traffic that used port 443, or HTTPS traffic. Here, I can locate the Client Hello packet to google.com in the HTTPS handshake process.
<p align="center">
<img width="570" alt="Untitled" src="https://github.com/chau-eric/beginner-wireshark/assets/76719902/48fc4c4e-180b-405b-be70-bacfa3e9968d"><br/>
</p>
If I wanted to find only the Client Hellos, I could use the display filter "tls.handshake.type==1."
<p align="center"><img width="564" alt="Untitled" src="https://github.com/chau-eric/beginner-wireshark/assets/76719902/02f667d3-5ec4-484b-8005-823ddeaffefc"><br/>
</p>
Regardless of which of the two filters I used, I was able to detect google.com's IP address 172.253.62.105, which is listed as the destination IP in the packet capture. Now I can use that destination IP in a new display filter "ip.addr==172.253.62.105" to find all the network traffic between google.com.
<p align="center"><img width="564" alt="Untitled" src="https://github.com/chau-eric/beginner-wireshark/assets/76719902/cccd3143-d392-4e07-9c4a-80b7cd1c652c"><br/></p>
Notice how all the packets have either a destination IP address of 172.253.62.105 (packets sent TO google.com) or a source IP address of 172.253.62.105 (packets sent FROM google.com). If I wanted just the packets FROM google.com, I can change the display filter to "ip.src==172.253.62.105."
<p align="center"><img width="564" alt="Untitled" src="https://github.com/chau-eric/beginner-wireshark/assets/76719902/f78d7856-5061-49e4-94d2-ee2f3a76ad1d"><br/></p>
Likewise, I can get just the packets TO google.com using the filter "ip.dst==172.253.62.105."

<h4>Locate all HTTPS packets from a capture not containing a certain IP address</h4>
First, I started another packet capture in Wireshark on my device's ethernet port. This time, I opened up two tabs in my web browser, google.com and duckduckgo.com. I stopped the capture and saved the file to task.pcapng. I then used the same display filter from the previous part to display only the Client Hellos, "tls.handshake.type==1."
<p align="center"><img width="559" alt="Untitled" src="https://github.com/chau-eric/beginner-wireshark/assets/76719902/d8c7d5a8-44b9-45fd-bb14-c304315381f8"><br/></p>
Let's say that first destination IP, 172.253.115.100 (google.com), is what I don't want. In other words, I want to display all the web traffic that isn't to or from google.com. To do this, I will use a more advanced display filter, "!(ip.addr==172.253.115.100) and (tcp.port==443 or tcp.port==80)."
<p align="center"><img width="558" alt="Untitled" src="https://github.com/chau-eric/beginner-wireshark/assets/76719902/1659fe70-97f1-471c-8a98-6c1967624c2e"><br/></p>
Let's break down this display filter. First, I select all packets to and from google.com with "ip.addr==172.253.115.100." I then use the NOT operator (!) on that expression to display all the packets that aren't to or from google.com. The second part of the display filter, "tcp.port==443 or tcp.port==80," displays only HTTPS and HTTP packets. The OR operator here means that both are displayed. Finally, the AND operator means that I want both of these expressions to be true. In other words, I want all the HTTPS and HTTP traffic that is also not to or from google.com. Notice how there are no packets being displayed with the IP address 172.253.115.100. This means that the display filter was successful in displaying all HTTPS and HTTP traffic not to or from google.com.
