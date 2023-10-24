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
<h2>Adversary (part 1)</h2>
I created the C2 payload (named "SPONTANEOUS_LATEX.exe") on the Ubuntu VM and downloaded it directly onto the Windows VM. After executing the payload, I could now interact directly with the C2 session on the Windows VM. Executing the commands <b>info</b> and <b>whoami</b> gives me basic information about the session and the user.
<p align="center"><img width="1278" alt="Untitled" src="https://github.com/chau-eric/LimaCharlie-Lab/assets/76719902/0aae3673-1070-4893-9086-552ab90d2c00"><br/>
</p>
More importantly, I can also see the victim's running processes using <b>ps -T</b>. Sliver automatically marks its own process in green (SPONTANEOUS_LATEX.exe) and any defensive tools in red (Sysmon).
<p align="center"><img width="259" alt="Untitled" src="https://github.com/chau-eric/LimaCharlie-Lab/assets/76719902/2463da5a-18b4-44fa-a983-2befe9f69f72"><br/>
</p>
From the Ubuntu VM, I can also execute the command <b>procdump -n lsass.exe -s lsass.dmp</b>. This is a common method to obtain a victim's OS credentials in the Windows Local Security Authority Subsystem Service (LSASS) and save it locally to your Sliver C2 server.

<h2>Detection on LimaCharlie</h2>
Using LimaCharlie to view all processes on the Windows VM, I can see the C2 payload is one of the only unsigned processes running. I can also see that it's active on the network and its source and destination IP.
<p align="center"><img width="1039" alt="Untitled" src="https://github.com/chau-eric/LimaCharlie-Lab/assets/76719902/b4baf96b-a202-4215-98a1-8d6ad61f6bc4"><br/>
</p>
The procdump command I ran earlier deals with lsass.exe, a known sensitive process. Using the <b>SENSITIVE_PROCESS_ACCESS</b> event filter, I can easily find the logs generated from the adversary.
<p align="center"><img width="1081" alt="Untitled" src="https://github.com/chau-eric/LimaCharlie-Lab/assets/76719902/b17ed47a-87c1-4b1d-8662-a8acea3a35a6"><br/>
</p>
Now that I know what the event looks like, I can create a detection & response rule to alert me anytime someone tries to access the victim's credentials again.
<p align="center"><img width="657" alt="Untitled" src="https://github.com/chau-eric/LimaCharlie-Lab/assets/76719902/100efa99-8130-409a-b835-ef36fa4fdfb6"><br/>
</p>
This rule will detect SENSITIVE_PROCESS_ACCESS events where the process ends with "lsass.exe". When detected, it will respond by generating a report called "LSASS Access."
<p align="center"><img width="1072" alt="Untitled" src="https://github.com/chau-eric/LimaCharlie-Lab/assets/76719902/ac196e3e-1454-40b5-8a53-215581b75be6"><br/>
</p>
After running the <b>procdump -n lsass.exe -s lsass.dmp</b> command again from the attacker, I can see the threat detected using the signature I just created.

<h2>Blocking attacks</h2>
**WIP**
