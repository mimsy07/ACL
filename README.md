<h1>Access Control List (ACL)</h1>


<h2>Description</h2>
Designed and implemented extended ACL security policies in CISCO PACKET TRACER to control inter-network communication, restrict unauthorized traffic.
The network consists of two company networks, an ISP network, and 5 routers acting as servers.
<br />


<h3>The network is divided into separate subnets</h3>

- <b>Company 1: 192.168.10.0/24</b> 
- <b>Company 2: 172.16.10.0/24</b>
- <b>ISP: 20.30.10.0/24</b>
- <b>Server Network: 200.10.20.0/24</b>

<h3>Main Objectives</h3>

 1. <b>Restrict unauthorized traffic</b> based on source/destination IP network address, when required, specific protocols or ports.
 2. <b>Protects critical servers</b> such as the DNS and web servers by configuring SSH.
 3. <b>Demonstrates basic security layer</b> by filtering and control traffic through servers, while maintaining required communication between users

<h3>Key Skills Demonstrated</h3>

-  CISCO IOS ACL configuration
-  Extended ACL implementation
-  ACL troubleshooting and verification
-  Testing connectivity
-  Assigning designated IP addresses to the interface
-  Implementing EIGRP routing protocol
-  Configure passive interface, for router interface who's not sending hello message
-  Setup and configure SSH

<h2>Project Walk through</h2>

<p align="center">
Network Diagram: <br/>
<img src="https://github.com/mimsy07/ACL/blob/main/ACL.png" height="80%" width="80%"/>
<br />
<br />

---

## Task

|SCENARIO: IN CO-1 |
|:-------------------|
| 1. Allow R-PC to TELNET WEB-1 and WEB-2 |
| 2. Allow R-PC to SSH WEB-1 and WEB-2 |
| 3. Allow R-PC to HTTP WEB-1 and WEB-2 |
| 4. Allow R-PC to HTTPS WEB-1 and WEB-2 |
| 5. Allow network 192.168.10.0/24 to PING DNS-SERVER & SERVER-1 |
| 6. Allow R-PC to PING, TELNET, SSH, HTTP, & HTTPS SERVER-1 |

<br>

|SCENARIO: IN CO-2 |
|:-------------------|
| 1. Allow USER_PC to TELNET INTERNAL_SERVER |
| 2. Allow USER_PC to SSH INTERNAL_SERVER |
| 3. Allow USER_PC to HTTP INTERNAL_SERVER |
| 4. Allow USER_PC to HTTPS INTERNAL_SERVER |
| 5. Allow USER_PC to PING entire network 172.16.10.0/24 |
 
## Configurations

<h3><b>CO-1</b></h3>

<h4><b>Access Control List</b></h4>

```
conf t
access-list 150 permit tcp host 192.168.10.100 host 200.10.20.30 eq telnet
access-list 150 permit tcp host 192.168.10.100 host 200.10.20.40 eq telnet
access-list 150 permit tcp host 192.168.10.100 host 200.10.20.30 eq 22
access-list 150 permit tcp host 192.168.10.100 host 200.10.20.40 eq 22
access-list 150 permit tcp host 192.168.10.100 host 200.10.20.30 eq www
access-list 150 permit tcp host 192.168.10.100 host 200.10.20.40 eq www
access-list 150 permit tcp host 192.168.10.100 host 200.10.20.30 eq 443
access-list 150 permit tcp host 192.168.10.100 host 200.10.20.40 eq 443
access-list 150 permit icmp 192.168.10.0 0.0.0.255 host 200.10.20.20
access-list 150 permit icmp 192.168.10.0 0.0.0.255 host 200.10.20.50
access-list 150 permit tcp host 192.168.10.100 host 200.10.20.50 eq telnet
access-list 150 permit tcp host 192.168.10.100 host 200.10.20.50 eq 22
access-list 150 permit tcp host 192.168.10.100 host 200.10.20.50 eq www
access-list 150 permit tcp host 192.168.10.100 host 200.10.20.50 eq 443
access-list 150 permit eigrp any any
access-list 150 permit tcp host 200.10.20.30 host 192.168.10.100 established
access-list 150 permit tcp host 200.10.20.40 host 192.168.10.100 established
access-list 150 permit tcp host 200.10.20.50 host 192.168.10.100 established
exit

interface GigabitEthernet0/1
ip access-group 150 out
exit
```
<h4><b>EIGRP ROUTING PROTOCOL</b></h4>

```
router eigrp 100
 passive-interface default
 no passive-interface GigabitEthernet0/0
 no passive-interface GigabitEthernet0/1
 network 192.168.10.0 255.255.255.0
 network 20.30.10.0 0.255.255.255.0
 no auto-summary
 exit
```
<h3><b>CO-2</b></h3>

<h4><b>Access Control List (Applied named ACL in CO-2)</b></h4>

```
ip access-list extended cmpny-rules
 permit tcp host 200.10.20.60 host 172.16.10.100 eq telnet
 permit tcp host 200.10.20.60 host 172.16.10.100 eq 22
 permit tcp host 200.10.20.60 host 172.16.10.100 eq www
 permit tcp host 200.10.20.60 host 172.16.10.100 eq 443
 permit icmp host 200.10.20.60 172.16.10.0 0.0.0.255
 permit eigrp any any

interface GigabitEthernet0/1
ip access-group cmpny-rules in
exit
```

<h4><b>EIGRP ROUTING PROTCOL</b></h4>

```
router eigrp 100
 passive-interface default
 no passive-interface GigabitEthernet0/0
 no passive-interface GigabitEthernet0/1
 network 172.16.10.0 255.255.255.0
 network 20.30.10.0 255.255.255.0
 no auto-summary
```

<h4><b>IMPLEMENTING SSH TO INTERNAL-SERVER (CO-2)</b></h4>

```
username admin secret cisco            //secret for md5 security
ip domain-name internal-server.com
crypto key generate rsa
1024                                   //for encryption
ip ssh version2

line vty 0 4
login local
transport input all                  //to accept both telnet and ssh
exit
```

<h3><b>DNS-SERVER</b></h3>

<h4><b>EIGRP ROUTING PROTOCOL</b></h4>

```
router eigrp 100
 passive-interface default
 no passive-interface GigabitEthernet0/0
 network 200.10.20.0 255.255.255.0
exit

```

<h4><b>SSH</b></h4>

```
username admin secret cisco        //secret for md5 security
ip domain-name DNS-Server.com
crypto key generate rsa
1024                              //for encryption
ip ssh version 2

line vty 0 4
login local
transport input all              //for SSH and telnet
exit
```
<h3><b>WEB-1</b></h3>

<h4><b>EIGRP ROUTING PROTOCOL</b></h4>

```
router eigrp 100
 passive-interface default
 no passive-interface GigabitEthernet0/0
 network 200.10.20.0 255.255.255.0
exit
```
<h4><b>SSH</b></h4>

```
username admin secret cisco        //secret for md5 security
ip domain-name web-1.com
crypto key generate rsa
1024                              //for encryption
ip ssh version 2

line vty 0 4
login local
transport input all              //for SSH and telnet remote access
exit
```
<h3><b>WEB-2</b></h3>

<h4><b>EIGRP ROUTING PROTOCOL</b></h4>

```
router eigrp 100
 passive-interface default
 no passive-interface GigabitEthernet0/0
 network 200.10.20.0 255.255.255.0
exit
```
<h4><b>SSH</b></h4>

```
username admin secret cisco        //secret for md5 security
ip domain-name web-2.com
crypto key generate rsa
1024                              //for encryption
ip ssh version 2

line vty 0 4
login local
transport input all              //for SSH and telnet
exit
```
<h3><b>SERVER-1</b></h3>

<h4><b>EIGRP ROUTING PROTOCOL</b></h4>

```
router eigrp 100
 passive-interface default
 no passive-interface GigabitEthernet0/0
 network 200.10.20.0 255.255.255.0
exit

```

<h4><b>SSH</b></h4>

```
username admin secret cisco        //secret for md5 security
ip domain-name server-1.com
crypto key generate rsa
1024                              //for encryption
ip ssh version 2

line vty 0 4
login local
transport input all              //to accept both SSH and telnet remote access
exit
```
<!--
Select the disk:  <br/>
<img src="https://i.imgur.com/tcTyMUE.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Enter the number of passes: <br/>
<img src="https://i.imgur.com/nCIbXbg.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Confirm your selection:  <br/>
<img src="https://i.imgur.com/cdFHBiU.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Wait for process to complete (may take some time):  <br/>
<img src="https://i.imgur.com/JL945Ga.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Sanitization complete:  <br/>
<img src="https://i.imgur.com/K71yaM2.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Observe the wiped disk:  <br/>
<img src="https://i.imgur.com/AeZkvFQ.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>


 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
