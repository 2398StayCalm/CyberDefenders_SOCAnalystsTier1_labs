# Yellow RAT
Link to the lab -> [https://cyberdefenders.org/blueteam-ctf-challenges/yellow-rat/]


### Scenario:
During a regular IT security check at GlobalTech Industries, abnormal network traffic was detected from multiple workstations. Upon initial investigation, it was discovered that certain employees' search queries were being redirected to unfamiliar websites. This discovery raised concerns and prompted a more thorough investigation. Your task is to investigate this incident and gather as much information as possible.

### Questions:

#### Q1: Understanding the adversary helps defend against attacks. What is the name of the malware family that causes abnormal network traffic?

We were provided with a malware hash for analysis. For the first step I would suggest that we check this hash on VirusTotal. There we can go to the Community tab and there we can see correlation with a lab name, that should be our answer :)

<img width="1517" height="717" alt="image" src="https://github.com/user-attachments/assets/76a3cbf7-cd5b-4fad-bdb1-3362dee2e4ff" />

It worked! :)

#### Q2: As part of our incident response, knowing common filenames the malware uses can help scan other workstations for potential infection. What is the common filename associated with the malware discovered on our workstations?

This one is actually very easy because the answer is already in front of us. The filename is presented right under the analyzed hash.

<img width="800" height="200" alt="image" src="https://github.com/user-attachments/assets/63b32d6e-ce7f-4954-8b6f-6c55d02b1861" />

It worked! :)

#### Q3: Determining the compilation timestamp of malware can reveal insights into its development and deployment timeline. What is the compilation timestamp of the malware that infected our network?

To determine timestamps we're going to Details tab. There we have lots of informations about the malware. When we scroll down we can discover Portable Executable Info that is usually tied to Windows executables and libraries. There we have a Compilation time.

<img width="758" height="267" alt="image" src="https://github.com/user-attachments/assets/dca514ee-d8a1-45bc-bd7e-ea0a0fba5674" />

It worked! :)

#### Q4: Understanding when the broader cybersecurity community first identified the malware could help determine how long the malware might have been in the environment before detection. When was the malware first submitted to VirusTotal?

This information is also easy to find exactly in the same tab Details. We have to find History of the file and there it is!

<img width="677" height="187" alt="image" src="https://github.com/user-attachments/assets/79308884-b600-44e2-9ace-e313b9ae2465" />

It worked! :)

#### Q5: To completely eradicate the threat from Industries' systems, we need to identify all components dropped by the malware. What is the name of the .dat file that the malware dropped in the AppData folder?

In this one I suggest to go to the Hybrid Analysis site and check the hash over there. We are provided with 3 results where the second one is about the exact hash as a main subject. Going there we scroll down until we se data about Extracted Strings. We can scroll down or just use the Ctr+F and search for the extension ".dat".

<img width="877" height="553" alt="image" src="https://github.com/user-attachments/assets/5180e955-d660-4c07-a558-1744fe9efbdb" />

It worked! :)

#### Q6: It is crucial to identify the C2 servers with which the malware communicates to block its communication and prevent further data exfiltration. What is the C2 server that the malware is communicating with?

Here we can go back to VT and check Behavior tab. There we look for Memory Pattern Urls if it gives us some information.

<img width="902" height="87" alt="image" src="https://github.com/user-attachments/assets/07abccfd-5921-4f32-96a7-9361f7e7e284" />

It worked! :)
