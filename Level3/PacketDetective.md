# 💻 PacketDetective
📌Link to the lab -> [https://cyberdefenders.org/blueteam-ctf-challenges/packetdetective/]

### 📓 Scenario:
In September 2020, your SOC detected suspicious activity from a user device, flagged by unusual SMB protocol usage. Initial analysis indicates a possible compromise of a privileged account and remote access tool usage by an attacker.

Your task is to examine network traffic in the provided PCAP files to identify key indicators of compromise (IOCs) and gain insights into the attacker’s methods, persistence tactics, and goals. Construct a timeline to better understand the progression of the attack by addressing the following questions.

### ❓Questions❓:

#### ❔  1: The attacker’s activity showed extensive SMB protocol usage, indicating a potential pattern of significant data transfer or file access.
What is the total number of bytes of the SMB protocol?

After we deploy our Wireshark we open the first PCAPNG file. In the window menu we choose Statistics tab and go to the Protocol Hierarchy. There we see types of packets and more informations about the volume of those packets.

<img width="1917" height="372" alt="image" src="https://github.com/user-attachments/assets/a124d20f-14ed-4926-8b9e-5a58dd7a7958" />

It worked! 🔥

#### ❔  2: Authentication through SMB was a critical step in gaining access to the targeted system. Identifying the username used for this authentication will help determine if a privileged account was compromised.
Which username was utilized for authentication via SMB?

Here we filter for NTLMSSP authorization protocol and we can see in clear text the username that we look for. 

<img width="1790" height="362" alt="image" src="https://github.com/user-attachments/assets/bbb65922-05b3-4df2-bd10-287626129354" />

It worked! 🔥

#### ❔  3: During the attack, the adversary accessed certain files. Identifying which files were accessed can reveal the attacker's intent.
What is the name of the file that was opened by the attacker?

To get the name of the file we search with CTR+f for a string "path" and since there are not many packets we quickly spot the file that could interests the attacker in clear text.

<img width="1722" height="353" alt="image" src="https://github.com/user-attachments/assets/e39c3f8a-b925-40c5-a42a-f27e12a58044" />

It worked! 🔥

#### ❔  4: Clearing event logs is a common tactic to hide malicious actions and evade detection. Pinpointing the timestamp of this action is essential for building a timeline of the attacker’s behavior.
What is the timestamp of the attempt to clear the event log? (24-hour UTC format)

We see that besides normal SMB packets there are some EVENTLOG protocols related. We scroll down a bit and we can clearly see a request to clear Event Logs. Now we need to dig a little deeper in infomations of that packet.

<img width="1297" height="437" alt="image" src="https://github.com/user-attachments/assets/30574c1d-70b5-4e00-84cd-4465a70da2a4" />

It worked! 🔥

#### ❔  5: The attacker used "named pipes" for communication, suggesting they may have utilized Remote Procedure Calls (RPC) for lateral movement across the network. RPC allows one program to request services from another remotely, which could grant the attacker unauthorized access or control.
What is the name of the service that communicated using this named pipe?

Here we are looking at DCERPC protocol which is related with remote communication between a local computer and remote server. We get the first packet that we found and follow the TCP Stream.

<img width="1542" height="731" alt="image" src="https://github.com/user-attachments/assets/fdb4f37d-a7d4-4768-810a-4fd0b2ded0b9" />

After that we search for antyhing related to pipe.

<img width="1277" height="627" alt="image" src="https://github.com/user-attachments/assets/6a6c101a-0902-4dc1-8eee-7329f8ef678e" />

It worked! 🔥

#### ❔  6: Measuring the duration of suspicious communication can reveal how long the attacker maintained unauthorized access, providing insights into the scope and persistence of the attack.
What was the duration of communication between the identified addresses 172.16.66.1 and 172.16.66.36?

We go to the Statistics tab and choose Conversations. There we have different network layer protocols that identify devices and route traffic. The one that interests us is IPv4. We go to IPv4 tab and there we have our duration of communication between thees two addresses.

<img width="1900" height="261" alt="image" src="https://github.com/user-attachments/assets/b2fc5c44-786d-4dd5-85be-88706309e6fd" />

It worked! 🔥

#### ❔  7: The attacker used a non-standard username to set up requests, indicating an attempt to maintain covert access. Identifying this username is essential for understanding how persistence was established.
Which username was used to set up these potentially suspicious requests?

This one is actually easy to spot. After opening the third PCAPNG file we see authentication attempt right away. There is also our suspicious request with a suspicious username in clear text.

<img width="1486" height="290" alt="image" src="https://github.com/user-attachments/assets/48f82f89-de15-47fd-b0aa-084921b45b60" />

It worked! 🔥

#### ❔  8: The attacker leveraged a specific executable file to execute processes remotely on the compromised system. Recognizing this file name can assist in pinpointing the tools used in the attack.
What is the name of the executable file utilized to execute processes remotely?

The file is also visible right after we open a PCAPNG file. The information is in clear text.

<img width="1522" height="254" alt="image" src="https://github.com/user-attachments/assets/fe1d073c-d679-46cd-bf5d-97247ef05252" />

It worked! 🔥

We've done it! 🥇
