# 💻 Tomcat Takeover
📌Link to the lab -> [https://cyberdefenders.org/blueteam-ctf-challenges/tomcat-takeover/]

### 📓 Scenario:
The SOC team has identified suspicious activity on a web server within the company's intranet. To better understand the situation, they have captured network traffic for analysis. The PCAP file may contain evidence of malicious activities that led to the compromise of the Apache Tomcat web server. Your task is to analyze the PCAP file to understand the scope of the attack.

### ❓Questions❓:

#### ❔  1: _Given the suspicious activity detected on the web server, the PCAP file reveals a series of requests across various ports, indicating potential scanning behavior. Can you identify the source IP address responsible for initiating these requests on our server?_

Right after we open a PCAP file in Wireshark we go to the Statistics tab and look at Conversations. There we need to check IPv4 addresses and we can see which address sent the biggest chunk of packets.

<img width="1790" height="397" alt="1" src="https://github.com/user-attachments/assets/32cdd10a-414b-492a-9fdf-dc47d358a7c6" />


It worked! 🔥

#### ❔  2: _Based on the identified IP address associated with the attacker, can you identify the country from which the attacker's activities originated?_

Now that we know the attacker's IP Address we can quickly find out about its country from free open source like AbuseIPDB.

<img width="652" height="320" alt="1" src="https://github.com/user-attachments/assets/409371e4-f03d-4c7e-be64-05bcd1b487d6" />

It worked! 🔥

#### ❔  3: _From the PCAP file, multiple open ports were detected as a result of the attacker's active scan. Which of these ports provides access to the web server admin panel?_

We filter by attacker's IP Address and try to look for a string "admin" which is in the url usually as a directory.

<img width="1461" height="772" alt="image" src="https://github.com/user-attachments/assets/9e1325ca-c8b3-4206-95e9-a5745e8a731f" />

It worked! 🔥

#### ❔  4: _Following the discovery of open ports on our server, it appears that the attacker attempted to enumerate and uncover directories and files on our web server. Which tools can you identify from the analysis that assisted the attacker in this enumeration process?_

For this we filter by the attacker's IP Address and HTTP protocol to see if there are multiple directories visited in a row. It can suggest enumeration process. Then we dig into one of those packets and see in detail the tool that was used as User Agent.

<img width="1055" height="758" alt="image" src="https://github.com/user-attachments/assets/e5bf02fa-db87-42ab-b33d-24583a40cba0" />

It worked ! 🔥

#### ❔  5: _After the effort to enumerate directories on our web server, the attacker made numerous requests to identify administrative interfaces. Which specific directory related to the admin panel did the attacker uncover?_

We filter by attacker's IP address as a source IP and HTTP protocol only. It gives as a smaller chunk of packets and when we scroll down we can quickly discover a directry related to admin panel. Its name is related to a function in most companies (so it would be even easier to spot).

<img width="1372" height="409" alt="image" src="https://github.com/user-attachments/assets/73df0883-88e3-4d16-9261-2fe0b9f3e6d8" />

It worked! 🔥

#### ❔  6: _After accessing the admin panel, the attacker tried to brute-force the login credentials. Can you determine the correct username and password that the attacker successfully used for login?_

We scroll down more and we see more enumeration of the specific administrative interface (as a directory). This is a good lead. Now we know that the attacker tried brute-forcing so we can assume that there were some failures in attempts. We search for "unauthorized" string and we see that there were some attempts. We scroll down a bit and there is a success so we know that the attacker succeded. Right after the code 200 of successful login attempt there is a file that confirms the Manager UI loaded successfully. We inspect that packet and when we scroll down we can discover interesting informations about credentials that were used.

<img width="1200" height="765" alt="image" src="https://github.com/user-attachments/assets/a8872057-1df5-432d-9991-a5b9fc8f4b12" />

It worked! 🔥

#### ❔  7: _Once inside the admin panel, the attacker attempted to upload a file with the intent of establishing a reverse shell. Can you identify the name of this malicious file from the captured data?_

So we know that right after successful login, an attacker tried to upload a file to get a connection persistence. We look for a POST method. Then we dig into details of that packet.

<img width="1238" height="386" alt="image" src="https://github.com/user-attachments/assets/bea7a7f6-0a62-41b0-bb97-213634892da7" />

It worked! 🔥

#### ❔  8: _After successfully establishing a reverse shell on our server, the attacker aimed to ensure persistence on the compromised machine. From the analysis, can you determine the specific command they are scheduled to run to maintain their presence?_

Here we need to focus on TCP connections after the malicious file was uploaded. We isolate it and follow the TCP Stream to get the command.

<img width="1283" height="422" alt="image" src="https://github.com/user-attachments/assets/e352d4eb-fe32-472a-874d-6db4a41dd70e" />

It worked! 🔥

We've done it! 🥇
