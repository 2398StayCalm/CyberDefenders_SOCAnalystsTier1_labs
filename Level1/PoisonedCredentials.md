# PoisonedCredentials
Link to the lab -> [https://cyberdefenders.org/blueteam-ctf-challenges/poisonedcredentials/]

### Scenario:

Your organization's security team has detected a surge in suspicious network activity. There are concerns that LLMNR (Link-Local Multicast Name Resolution) and NBT-NS (NetBIOS Name Service) poisoning attacks may be occurring within your network. These attacks are known for exploiting these protocols to intercept network traffic and potentially compromise user credentials. Your task is to investigate the network logs and examine captured network traffic.

### Questions:

#### Q1: In the context of the incident described in the scenario, the attacker initiated their actions by taking advantage of benign network traffic from legitimate machines. Can you identify the specific mistyped query made by the machine with the IP address 192.168.232.162?

Looks like we have to investigate some network traffic. We already have the attacker's IP Address and probably there is some kind of file to analyze. First we go to the Start here directory to see if we have some files or tools connected to this lab. In Artifacts directory we have a PCAP file, so my guess would be to open the Wireshark tool from Tools directory and there open the PCAP file. Once we open it we can filter the traffic by attacker's IP as a source address. Now we can see that in the scenario they've mentioned about two protocols that could be a potential link to the attack so let's filter the traffic by them also.

<img width="1512" height="613" alt="image" src="https://github.com/user-attachments/assets/e52a108e-89d2-4504-abe4-67d63ee38e54" />


We already see some suspicious query that was written and mistyped by the attacker.

It worked! :)

#### Q2: We are investigating a network security incident. To conduct a thorough investigation, We need to determine the IP address of the rogue machine. What is the IP address of the machine acting as the rogue entity?

When we get back to the raw traffic, we can see where is the packet that the attacker mistyped the query (47). Then chronologicaly we can see what happend in the traffic next. 192.168.232.162 sends a name query for a mistyped resource and because the query is invalid, legitimate name resolution fails. Next, 192.168.232.215 reponds to the query - it sends an mDNS response claimin ownership of the queried name then sends an NBNS name query response mapping the name to itself. It is a typical behavior of a rogue responder, which answers name resolution request it should not legitimately own. All responses are coming from 192.168.232.215 and are triggered by a mistyped query from the attacker machine.

<img width="1217" height="137" alt="image" src="https://github.com/user-attachments/assets/87d342c7-bf7a-4faa-b733-e75a84ad1156" />

It worked! :)

#### Q3: As part of our investigation, identifying all affected machines is essential. What is the IP address of the second machine that received poisoned responses from the rogue machine?

Let's filter the traffic so we can see all packets with the rogue machine. Now we already see the communication between the atacker's machine and rogue machine. We can see another Address that could potentialy point to a second affected machine that received poisoned reponses.

<img width="1137" height="382" alt="image" src="https://github.com/user-attachments/assets/2daf4a75-1df3-4fc1-ab01-e4cfbe6e2be6" />

It worked! :)

#### Q4: We suspect that user accounts may have been compromised. To assess this, we must determine the username associated with the compromised account. What is the username of the account that the attacker compromised?

We know that there is more than one account in organization so let's assume that there is some kind of domain. Now wecan filter our traffic to see if there was some NTLM authentication attempts. It occurs that there were! We can clearly see that someone tried to authenticate as a user of cybercactus.local domain.

<img width="1376" height="123" alt="image" src="https://github.com/user-attachments/assets/c805558f-b908-4a3c-922d-f76a7790604c" />

It worked! :)

#### Q5: As part of our investigation, we aim to understand the extent of the attacker's activities. What is the hostname of the machine that the attacker accessed via SMB?

Here we must again filter a whole traffic by SMB2 protocol to have a more clear view of what is going on. Just before authentication attempt, the attacker must've got onto machine so he can log in. We can check the one packet before the authentication attempt because we can see that the machine provided a NTLMSSP authentication challange so there could be some details about which machine. Now we have to do our research of where to the name of the host could be hidden and there it is!

<img width="1523" height="725" alt="image" src="https://github.com/user-attachments/assets/98ea5278-7aaf-4fed-b025-19683a775e44" />

It worked! :)
