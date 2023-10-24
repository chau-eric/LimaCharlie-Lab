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
<h2>Retrieving info via C2 session</h2>
I created the C2 payload on the Ubuntu VM and downloaded it directly onto the Windows VM. After executing the payload, I could now interact directly with the C2 session on the Windows VM. Executing the commands <b>info</b> and <b>whoami</b> gives me basic information about the session and the user.
<p align="center"><img width="1278" alt="Untitled" src="https://github.com/chau-eric/LimaCharlie-Lab/assets/76719902/0aae3673-1070-4893-9086-552ab90d2c00"><br/>
</p>
More importantly, I can also see the victim's running processes using <b>ps -T</b>. Sliver automatically marks its own process in green (SPONTANEOUS_LATEX.exe) and any defensive tools in red (Sysmon).
<p align="center"><img width="259" alt="Untitled" src="https://github.com/chau-eric/LimaCharlie-Lab/assets/76719902/2463da5a-18b4-44fa-a983-2befe9f69f72"><br/>
</p>

<h2>Investigating on LimaCharlie</h2>
Using LimaCharlie to view all processes on the Windows VM, I can see the C2 payload is one of the only unsigned processes running. I can also see that it's active on the network and its source and destination IP.
<p align="center"><img width="1039" alt="Untitled" src="https://github.com/chau-eric/LimaCharlie-Lab/assets/76719902/b4baf96b-a202-4215-98a1-8d6ad61f6bc4"><br/>
</p>
