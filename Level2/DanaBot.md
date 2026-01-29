# 💻 DanaBot
📌Link to the lab -> [https://cyberdefenders.org/blueteam-ctf-challenges/danabot/]

### 📓 Scenario:
The SOC team has detected suspicious activity in the network traffic, revealing that a machine has been compromised. Sensitive company information has been stolen. Your task is to use Network Capture (PCAP) files and Threat Intelligence to investigate the incident and determine how the breach occurred.

### ❓Questions❓:

#### ❔  1: Which IP address was used by the attacker during the initial access?

After opening a PCAP file in Wireshark at first sight we don't see anything wrong and suspicious. Even ports that are used look legit. Sometimes it's not about ports/protocols but about a context and behavior that we observe. The ports used (49786 and 80) are standard and commonly used for web traffic. However, when combined with automated connection timing, DNS resolution and the request to a suspicious endpoint their usage may indicate an attempt to disguise command-and-control communication as legitimate HTTP traffic. The first strong red flag appears where we can see a HTTP GET request for login.php. That is not what an OS does. It looks like application-level behavior. In this situation we can suspect unauthorized software activity. We should try with one of these IPs.

<img width="1897" height="257" alt="image" src="https://github.com/user-attachments/assets/85fd13bd-3037-400d-b015-3f2510260df7" />

It worked! 🔥

#### ❔  2: What is the name of the malicious file used for initial access?

In the first question we mentioned about a suspicious request of a specific file. We can follow the TCP stream to get more content. I think it was a great choice. The captured traffic shows an automated HTTP GET request which responds with a forced download of an obfuscated file. Despite appearing as a browser request via a spoofed User-Agent, the response behavior is inconsistent with legitimate login functionality.

<img width="1267" height="868" alt="image" src="https://github.com/user-attachments/assets/00c936c7-62f9-4910-901a-1daeb5169240" />

It worked! 🔥

#### ❔  3: What is the SHA-256 hash of the malicious file used for initial access?

The easiest way is to export the .php file and check its hash using terminal.

<img width="1472" height="707" alt="image" src="https://github.com/user-attachments/assets/95983bd8-4f4e-4545-ab6b-bcb64c2cfaef" />

It worked! 🔥

#### ❔  4: Which process was used to execute the malicious file?

We take our SHA256 hash and try to analyze it with VirusTotal. Now we have to get information about processes related to the JSON file. We go to Behavior tab and scroll down to Processes Tree. There we see familiar .js file and which process was related with that.

<img width="1222" height="292" alt="image" src="https://github.com/user-attachments/assets/91e9bd26-9ed6-44d2-92ee-bca7141a35c9" />

It worked! 🔥

#### ❔  5: What is the file extension of the second malicious file utilized by the attacker?

If we go to the Community tab, there is a ANY_RUN full analysis report. Then we serach for our process that executed a malicious file and we discover that there is another one except for JSON file. This is a bigger executable file.

<img width="1397" height="197" alt="image" src="https://github.com/user-attachments/assets/c039c4fd-9a1b-4dd3-8eba-44bd26c33ab7" />

It worked! 🔥

#### ❔  6: What is the MD5 hash of the second malicious file?

Now we look further and search for MD5 hash of that file in this report. It is pretty visible 🕵️

<img width="1358" height="163" alt="1" src="https://github.com/user-attachments/assets/f6dd6367-2fdf-45e0-8ff2-29af70d54cae" />

It worked! 🔥

We've done it! 🥇
