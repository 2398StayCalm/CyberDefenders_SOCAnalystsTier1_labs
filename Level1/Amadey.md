# 💻Amadey - APT-C-36

📌Link to the lab -> [https://cyberdefenders.org/blueteam-ctf-challenges/amadey-apt-c-36/]

### 📓 Scenario:
An after-hours alert from the Endpoint Detection and Response (EDR) system flags suspicious activity on a Windows workstation. The flagged malware aligns with the Amadey Trojan Stealer. Your job is to analyze the presented memory dump and create a detailed report for actions taken by the malware.

### ❓Questions❓:

#### ❔1: In the memory dump analysis, determining the root of the malicious activity is essential for comprehending the extent of the intrusion. What is the name of the parent process that triggered this malicious behavior?

In this lab we'll be using a tool called Volatility3. It is used to investigate what was happening on a system at the time a memory snapshot was taken, especially during incident response and malware analysis. First we can move memory dump file to the directory where our tool is placed. Then we open the terminal in the exact same directory and with python3 command we deploy our tool. Using right command we can display list of processes. 

<img width="1918" height="177" alt="image" src="https://github.com/user-attachments/assets/5124c0cc-a35a-427a-8a86-543eacfe8421" />

When we scroll down a little we don't se nothing strange. All processes seem to be legit, but if we look closely we can see that one is a little bit strange. The presence of a process named lssass.exe (PID 2748) indicates process masquerading, as it closely imitates the legitimate lsass.exe service. This misspelling strongly suggests malicious activity. To make sure we can scroll back up and we can see a legit lsass.exe process (508). We can do an additional research also about the legit process (it is a legitimate and critical Windows system process responsible for user authentication and security policy enforcement ).

<img width="1640" height="430" alt="image" src="https://github.com/user-attachments/assets/c18e6f37-94bd-43db-9b0a-118a6b8f8824" />

It worked! 🔥

#### ❔2: Once the rogue process is identified, its exact location on the device can reveal more about its nature and source. Where is this process housed on the workstation?

We're looking for a path of the suspicious process. To do that we'll need another proper command to view some logs. From the last question we were able to determine the processe's PID. Now we can assume that this process was executed somwhere in the Command Line. Let's check if there were some command correlated with our PID.

<img width="1685" height="52" alt="image" src="https://github.com/user-attachments/assets/f90cbe47-72c9-4fb0-84b3-c753e6693fff" />

It worked! 🔥

#### ❔3: Persistent external communications suggest the malware's attempts to reach out C2C server. Can you identify the Command and Control (C2C) server IP that the process interacts with?

Now to identify C2C server IP we can reconstruct active and recently closed network connections from memory, which is ideal for identifying C2 communication. To do that we use windows.netscan which does exactly what we need to do to check this kind of activity. To get a better view on it we can correlate network connections with cmd line. There we'll get the IP Address.

<img width="1887" height="150" alt="image" src="https://github.com/user-attachments/assets/acaafd6e-1277-4ec4-a6d2-29b9d1174e7f" />

It worked! 🔥

#### ❔4: Following the malware link with the C2C, the malware is likely fetching additional tools or modules. How many distinct files is it trying to bring onto the compromised workstation?

This task requires to scan for file objects in memory. To get a better result we can filter for suspicious locations commonly used by malware.

<img width="1898" height="120" alt="image" src="https://github.com/user-attachments/assets/39cc69d8-57e1-427f-bb34-60cfff687f07" />

We scroll down to see if there is anything interesing. First we can spot our malicious original file (acting as legit but functioning as a payload) lssass.exe. Then we scroll a bit further and we see another suspicious file in similar location.

<img width="1853" height="235" alt="image" src="https://github.com/user-attachments/assets/87348556-d284-4f89-a78d-879f3fbe817c" />

It worked! 🔥

#### ❔5: Identifying the storage points of these additional components is critical for containment and cleanup. What is the full path of the file downloaded and used by the malware in its malicious activity?

From experience, we can usually assume that there will be some attempt of downloading some library files. So first step would be to check if there are some suspicious .dll files somwhere in the similar location as payloads.

<img width="1897" height="73" alt="image" src="https://github.com/user-attachments/assets/b2177906-e212-4c4c-bdaa-af155d929ef5" />

It worked! 🔥

#### ❔6: Once retrieved, the malware aims to activate its additional components. Which child process is initiated by the malware to execute these files?

As we discovered the malicious file that was downloaded and now we know that this is a DLL file it's almost obvious which child process would be initiated to execute ths file. But to make sure we can again check the process list. It's right after the payload.

<img width="1895" height="385" alt="image" src="https://github.com/user-attachments/assets/8b81d101-c7ad-4c43-b695-2a6f7a4a8563" />

It worked! 🔥

#### ❔7: Understanding the full range of Amadey's persistence mechanisms can help in an effective mitigation. Apart from the locations already spotlighted, where else might the malware be ensuring its consistent presence?

Let's check all locations of our payload. What we discovered is that one of those locations is meant to be the primary repository for task definitions in Windows Task Scheduler. This is an evidence of persistence. This should perfectly fit into our task.

<img width="1812" height="157" alt="image" src="https://github.com/user-attachments/assets/3a386af9-e2ea-4797-85ca-374f95fe0007" />

It worked! 🔥

All Done! 🥇
