# 💻PsExec Hunt
📌Link to the lab -> [https://cyberdefenders.org/blueteam-ctf-challenges/psexec-hunt/]

### 📓 Scenario:
An alert from the Intrusion Detection System (IDS) flagged suspicious lateral movement activity involving PsExec. This indicates potential unauthorized access and movement across the network. As a SOC Analyst, your task is to investigate the provided PCAP file to trace the attacker’s activities. Identify their entry point, the machines targeted, the extent of the breach, and any critical indicators that reveal their tactics and objectives within the compromised environment.


### ❓Questions❓:

#### ❔  1: To effectively trace the attacker's activities within our network, can you identify the IP address of the machine from which the attacker initially gained access?

We open a PCAPNG file in Wireshark first. Then we filter the traffic by NTLMSSP protocol which shows us authentication attempts between machines. There we can see that one of IP Address is trying to get access to some other machine. Let's try it.

<img width="1907" height="186" alt="image" src="https://github.com/user-attachments/assets/f9c36a50-6792-49eb-ac98-a08259bad3bc" />

It worked! 🔥

#### ❔  2: To fully understand the extent of the breach, can you determine the machine's hostname to which the attacker first pivoted?

Now we have to dig in to get the victim hostname. So we look into the SMB2 session.

<img width="953" height="603" alt="image" src="https://github.com/user-attachments/assets/951303ef-b668-4b76-a527-34c2b914d32f" />

It worked! 🔥

#### ❔  3: Knowing the username of the account the attacker used for authentication will give us insights into the extent of the breach. What is the username utilized by the attacker for authentication?

This one we can find quickly right next to the authentication attempt without changing anything in filtering. If you look close we can spot the username in plain text or if we dig deeper into specific packet.

<img width="1202" height="596" alt="image" src="https://github.com/user-attachments/assets/7f59c882-1474-420f-b1da-9756c7d45924" />

It worked! 🔥

#### ❔  4: After figuring out how the attacker moved within our network, we need to know what they did on the target machine. What's the name of the service executable the attacker set up on the target?

We unfliter everything to see clearly all packets again and search with CTR+F by strings for ".exe" which is an excecutable extension. First one possibly will be our answer.

<img width="1553" height="372" alt="image" src="https://github.com/user-attachments/assets/5f696104-d67c-4e87-b63f-10157d6d8890" />

It worked! 🔥

#### ❔  5: We need to know how the attacker installed the service on the compromised machine to understand the attacker's lateral movement tactics. This can help identify other affected systems. Which network share was used by PsExec to install the service on the target machine?

A quick way to get it is to copy the name of that process and filter by it. Our executable must connect to a share before it can create/write a file. Now we need to identify Tree Connect Request and there the packet shows the exact name of the share.

<img width="1104" height="263" alt="image" src="https://github.com/user-attachments/assets/5c276b32-55c5-461a-9951-d18ff3e6e95b" />

It worked! 🔥

#### ❔  6: We must identify the network share used to communicate between the two machines. Which network share did PsExec use for communication?

An executable after being copied it no longer uses the same share. For communication it switches to a named pipe over SMB2. So now we're looking for SMB2 and for a string "pipe". That should point us the name of a share as our solution.

<img width="1067" height="782" alt="image" src="https://github.com/user-attachments/assets/a7351bf5-3943-4491-b170-4cca2091c07b" />

It worked! 🔥

#### ❔  7: Now that we have a clearer picture of the attacker's activities on the compromised machine, it's important to identify any further lateral movement. What is the hostname of the second machine the attacker targeted to pivot within our network?

Now we're back to the filter from first question and we're looking for a second authorization attempt. We scroll down a bit and there it is!

<img width="1365" height="691" alt="image" src="https://github.com/user-attachments/assets/d8ed319e-8fdc-4b41-afd0-681fc1dd02e6" />

It worked! 🔥

We've done it! 🥇
