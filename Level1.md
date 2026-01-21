## WEBSTRIKE LAB

Scenario:
  A suspicious file was identified on a company web server, raising alarms within the intranet. The Development team flagged the anomaly, suspecting potential malicious activity. To address the issue, 
  the network team captured critical network traffic and prepared a PCAP file for review.
  Your task is to analyze the provided PCAP file to uncover how the file appeared and determine the extent of any unauthorized activity.

Questions:

  #### Q1:Identifying the geographical origin of the attack facilitates the implementation of geo-blocking measures and the analysis of threat intelligence. From which city did the attack originate?

First we have to deploy VM. We know that the network team prepared a PCAP file for analysis. Now the first tool that comes to mind is **Wireshark**, so let's look for it.We can check the only directory that is visible on a Desktop "Start here". That can be a great first step. In this directory we can see two other directories called "Artifacts" and "Tools". Let's try the second one. There it is! :

<img width="1462" height="737" alt="image" src="https://github.com/user-attachments/assets/297efaf3-9f03-4e75-ad01-641bddee6d3c" />

After we deploy Wireshark first thing we can do is that we can check if there was any file opened recently. File -> Open Recent and there it is "WebStrike.pcap".HTTP traffic is transmitted in cleartext, which allows inspection of request and response metadata, including source IP addresses, URLs, and transferred files. Let's filter these logs then, by HTTP protocol. Now we're down to two addresses where one of them could potentialy be our attacker. 

We can use different tools to check the country of each address. Usually I use AbuseIPDB which is free and available if you have access to the Internet. So one of them was not found in thei database. The second one was found in database and it has 11% score in confidense of abuse. It also shows that this IP Address is from China (which can be a good tip). Under the country we can also discover a City.

<img width="767" height="537" alt="image" src="https://github.com/user-attachments/assets/dad507f4-b4b1-463c-997b-95138c526abc" />


It worked! :)

 #### Q2:Knowing the attacker's User-Agent assists in creating robust filtering rules. What's the attacker's Full User-Agent?

Now that we discovered the attacker's IP Address, we can filter all logs for this IP as source_ip address since we know that some malicious file was uploaded from there. Let's use a simple query here: ip.src==<attacker_ip> && http. Now we just have to dig a little bit deeper we can find a User-Agent:

<img width="862" height="256" alt="image" src="https://github.com/user-attachments/assets/08c449d8-01d5-4d90-90e4-3e4dd5dfc096" />

It worked! :)
  
 #### Q3:We need to determine if any vulnerabilities were exploited. What is the name of the malicious web shell that was successfully uploaded?

Now we have to take into consideration that web shells usually have specific extensions like ".php", ".asp", ".jsp", ".aspx". They can also have abnormal or double extensions so let's look through these logs if we can discover something suspicious. There are not many logs left after filtering them by src.ip and http so we can quickly trace one suspicious file with double extension:

<img width="1797" height="306" alt="image" src="https://github.com/user-attachments/assets/de22036e-6fff-4cc0-92a3-6e29b467abac" />

It worked! :)

 #### Q4:Identifying the directory where uploaded files are stored is crucial for locating the vulnerable page and removing any malicious files. Which directory is used by the website to store the uploaded files?

The answer is connected to the previous question where we can see full path of the malicious file.

It worked! :)

 #### Q5:Which port, opened on the attacker's machine, was targeted by the malicious web shell for establishing unauthorized outbound communication?

To discover which port was targeted, we can just get rid of the http protocol from filtering to check chronology of events after getting to malicious file. There we see that the packet capture indicates a successful TCP connection from <attacker_ip> to <target_ip> over port 8080, followed by HTTP requests to a PHP file in an uploads directory. This behavior is consistent with interaction with access to an uploaded web shell.

<img width="1761" height="337" alt="image" src="https://github.com/user-attachments/assets/0e04e93c-6e35-4f1c-b2ee-e94c87909e93" />

It worked! :)

 #### Q6:Recognizing the significance of compromised data helps prioritize incident response actions. Which file was the attacker attempting to exfiltrate?

Here a simple http protocol filter shows execution of a malicious web shell and then we can see after establishing connection, an exfiltration of a file which looks like a system file on Unix/Linux systems that stores basic account information for all local users. It definitely means that the attacker is on to something (/etc/passwd).

<img width="1802" height="743" alt="image" src="https://github.com/user-attachments/assets/93a1316e-94b7-4591-abd1-4a6730dfb960" />

It worked! :)

