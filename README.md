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

<h2>Project setup:</h2>
There are two machines involved in this project: a Windows 11 VM and an Ubuntu Server 22.04.1. The Windows 11 VM will be my "victim" system that has Microsoft Defender disabled, and Sysmon and LimaCharlie installed. LimaCharlie has its own Endpoint Detection & Response telemetry, as well as the capability to ship Sysmon event logs. The Ubuntu machine is going to launch the adversarial attacks. It will do this using Sliver, a command-and-control (C2) framework.
<h4>Detect a web page's IP address using a display filter</h4>
I am going to start a packet capture in Wireshark on my device's ethernet port. While Wireshark is capturing packets, I am going to open the target web page in my web browser (I am using google.com for this project).
<p align="center">
<img width="570" alt="Untitled" src="https://github.com/chau-eric/beginner-wireshark/assets/76719902/9254201c-9e74-4b0c-b6dd-ae903e7acfa7"><br/>
</p>
After stopping the packet capture, I used the display filter "tcp.port==443" to display only the traffic that used port 443, or HTTPS traffic. Here, I can locate the Client Hello packet to google.com in the HTTPS handshake process.
<p align="center">
<img width="570" alt="Untitled" src="https://github.com/chau-eric/beginner-wireshark/assets/76719902/48fc4c4e-180b-405b-be70-bacfa3e9968d"><br/>
</p>
