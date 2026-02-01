# 💻 Ramnit
📌Link to the lab -> [https://cyberdefenders.org/blueteam-ctf-challenges/ramnit/]

### 📓 Scenario:
Our intrusion detection system has alerted us to suspicious behavior on a workstation, pointing to a likely malware intrusion. A memory dump of this system has been taken for analysis. Your task is to analyze this dump, trace the malware’s actions, and report key findings.



### ❓Questions❓:

#### ❔  1: What is the name of the process responsible for the suspicious activity?

We get a list of processes with "windows.pslist". Here it's hard to quickly spot the suspicious process. We have to do some research on it. Sometimes attackers hide malicious processes under legitimate names. We know that this is a list of processes from the time that attack was proceeded. So it is an attack time window. It is suspicious for normal installers to run in that time, especially browser installers 😈

<img width="1903" height="798" alt="image" src="https://github.com/user-attachments/assets/9bb2bbc5-1f3c-4c1a-bfdf-e193350c5dac" />

It worked! 🔥

#### ❔  2: What is the exact path of the executable for the malicious process?

Since we know the name of the malicious process w search for commands in the memory dump related to that executable.

<img width="1146" height="58" alt="image" src="https://github.com/user-attachments/assets/3b2ffbde-1ce2-480a-ac8a-36f22e376e14" />

It worked! 🔥

#### ❔  3: Identifying network connections is crucial for understanding the malware's communication strategy. What IP address did the malware attempt to connect to?

Here we use VirusTotal for the analysis but to do that we need HASH of that executable file. To get it we establish full path of that file and virtual address along the way. Using that wirtual address we're able to dump/extract this particular file and then check its SHA256 HASH. After that - to VT.

<img width="1033" height="96" alt="image" src="https://github.com/user-attachments/assets/924663e8-6174-4b92-85a6-e5eb83c5425e" />
<img width="1082" height="138" alt="image" src="https://github.com/user-attachments/assets/77ba9557-c03b-4f81-a700-12444766fb0e" />
<img width="1181" height="65" alt="image" src="https://github.com/user-attachments/assets/a0cbf0a1-5672-4d99-a78a-5d5c6bf5ff45" />
<img width="515" height="292" alt="image" src="https://github.com/user-attachments/assets/328067a5-0396-4ccc-b206-7d91d8703bfe" />


It worked! 🔥

#### ❔  4: To determine the specific geographical origin of the attack, Which city is associated with the IP address the malware communicated with?

After we got the IP Address that was associated with communication with malware we use AbuseIPDB to establish its City.

<img width="818" height="396" alt="image" src="https://github.com/user-attachments/assets/04205e9a-b889-4801-ac39-1f2ee3a148e4" />

It worked! 🔥

#### ❔  5: Hashes serve as unique identifiers for files, assisting in the detection of similar threats across different machines. What is the SHA1 hash of the malware executable?

This information is also available in the results of VirusTotal analysis. We just go to Details tab and see the hash.

<img width="1472" height="750" alt="image" src="https://github.com/user-attachments/assets/d1ca8fed-90cd-4309-aba6-af5ad15bb177" />

It worked! 🔥

#### ❔  6: Examining the malware's development timeline can provide insights into its deployment. What is the compilation timestamp for the malware?

For this we also search in the Details tab in VirusTotal. We just scroll down to Header and there it is.

<img width="927" height="153" alt="image" src="https://github.com/user-attachments/assets/8263932d-ee8e-4097-8093-e22c2e16f743" />

It worked! 🔥

#### ❔  7: Identifying the domains associated with this malware is crucial for blocking future malicious communications and detecting any ongoing interactions with those domains within our network. Can you provide the domain connected to the malware?

This one is to find on VirusTotal analysis also in Behavior tab. We scroll down to DNS Resolutions and we got it.

<img width="512" height="271" alt="image" src="https://github.com/user-attachments/assets/f1c39695-96bf-4ed4-9ac5-aa1e1cc179ea" />

It worked! 🔥

We've done it! 🥇
