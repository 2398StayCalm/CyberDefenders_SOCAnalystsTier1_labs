# 💻 XXE Infiltration
📌Link to the lab -> [https://cyberdefenders.org/blueteam-ctf-challenges/xxe-infiltration/]

### 📓 Scenario:
An automated alert has detected unusual XML data being processed by the server, which suggests a potential XXE (XML External Entity) Injection attack. This raises concerns about the integrity of the company's customer data and internal systems, prompting an immediate investigation.

Analyze the provided PCAP file using the network analysis tools available to you. Your goal is to identify how the attacker gained access and what actions they took.

### ❓Questions❓:

#### ❔  1: _Identifying the open ports discovered by an attacker helps us understand which services are exposed and potentially vulnerable. Can you identify the highest-numbered port that is open on the victim's web server?_

First we need to identify a victims server. So as we open the PCAP file in Wireshark and scroll down a bit we can see some communication and that one of the IP Addresses sends SYN,ACK flag from a well known port. It points to our victims server. Now we need to filter out this kind of communication and we know that if after SYN,ACK is ACK as an answer we assume that the port is open. So let's filter the traffic.

<img width="1566" height="270" alt="image" src="https://github.com/user-attachments/assets/54e0fd17-f5cc-4461-ba28-7980868bef82" />

As we can see there is a lot of communication to the port 80 of the victim's server. We can scroll down and see if there is even a higher one.

<img width="1647" height="455" alt="image" src="https://github.com/user-attachments/assets/b0cc219e-6e9f-4376-96ef-6bc6c846aaa3" />

It worked! 🔥

#### ❔  2: _By identifying the vulnerable PHP script, security teams can directly address and mitigate the vulnerability. What's the complete URI of the PHP script vulnerable to XXE Injection?_

As we know the attacker's IP address and we know that the PHP file that we're looking for is a vulnerability that can be use for XXE injection, then we have to look for POST request method from the attacker's side to clear the traffic and filter for such files.

<img width="933" height="152" alt="image" src="https://github.com/user-attachments/assets/98c2ef48-d95b-4b0e-b5da-ce5c751717f3" />

It worked! 🔥

#### ❔  3: _To construct the attack timeline and determine the initial point of compromise. What's the name of the first malicious XML file uploaded by the attacker?_

Looking at the result from the last filtering we go to details of the first packet where a PHP file was used and we see a content-type: multipart/from-data. This means that a file is being sent to the server. We dig deeper and discover the exact file that was transferred.

<img width="961" height="675" alt="image" src="https://github.com/user-attachments/assets/ea3aa4f2-0975-4c1a-ba4d-16bfb296d3c0" />

It worked! 🔥

#### ❔  4: _Understanding which sensitive files were accessed helps evaluate the breach's potential impact. What's the name of the web app configuration file the attacker read?_

We can see that the attacker used vulnerable PHP file for XXE Injection more than once and in the last case it manipulated with the ///etc/passwd. Now we can check if there were some more files. There is one that looks like a configuration file when we look closely into details.

<img width="952" height="647" alt="image" src="https://github.com/user-attachments/assets/78b5a333-7d3b-418e-9537-5fb86bd9cc8c" />

It worked! 🔥

#### ❔  5: _To assess the scope of the breach, what is the password for the compromised database user?_

Now we can take the packet that we doscovered in the last question and follow the HTTP Stream to see the attacker tried to manipulate configuration file using user credentials.

<img width="822" height="457" alt="image" src="https://github.com/user-attachments/assets/3327ded8-ec08-42a6-b73e-ffce28642556" />

It worked! 🔥

#### ❔  6: _Following the database user compromise. What is the timestamp of the attacker's initial connection to the MySQL server using the compromised credentials after the exposure?_

Here we display whole network traffic and search for our packet related to PHP configuration file. Then we scroll down a bit untill we can see a communication successful attempt. In details we find exact timestamp of that event.

<img width="1602" height="356" alt="image" src="https://github.com/user-attachments/assets/96233184-f7d9-4e08-a65e-a0951cc15d30" />

It worked! 🔥

#### ❔  7: _To eliminate the threat and prevent further unauthorized access, can you identify the name of the web shell that the attacker uploaded for remote code execution and persistence?_

We know that there were some more files uploaded by the attacker using a vulnerable PHP file. So assuming that this happened at the end it should be the last file that was sent this way. I'll leave that to you but to give a small hint, there are command executions related to that file.

<img width="1378" height="130" alt="image" src="https://github.com/user-attachments/assets/3554fd41-d35d-4b83-a735-b40346b39fa7" />

It worked! 🔥

We've done it! 🥇
