# OSKI LAB 
Link to the lab -> [https://cyberdefenders.org/blueteam-ctf-challenges/oski/]

### Scenario:

  The accountant at the company received an email titled "Urgent New Order" from a client late in the afternoon. When he attempted to access the attached invoice, he discovered it contained false order information. Subsequently, the SIEM solution generated an alert regarding downloading a potentially malicious file. Upon initial investigation, it was found that the PPT file might be responsible for this download. Could you please conduct a detailed examination of this file?

### Questions:

#### Q1: Determining the creation time of the malware can provide insights into its origin. What was the time of malware creation?

First thing that I recommend, since we have no idea what kind of file we're gonna get, is to open our VM with correct network configuration and try to download the file on the VM. Once we get there, we can unpack (unzip <filename>) the file to the directory of our choice. Then we see that there is a txt file with a hash:

<img width="646" height="515" alt="image" src="https://github.com/user-attachments/assets/d0663af0-75d0-40b0-860a-a3eb869e0238" />

We are asked to check what was the time of malware creation. For that we can just use VirusTotal, so let's do it! Copy the MD5 hash and paste it into VT. There we can definitely say that this is a malicious file. The score on that file is 60/71! Right but let's get back to our goal. We go to the Details tab and as we can see there it is - creation time:

<img width="1511" height="731" alt="image" src="https://github.com/user-attachments/assets/712bbeef-8e96-4c0e-8234-8613e432ab42" />

It worked! :)

#### Q2: Identifying the command and control (C2) server that the malware communicates with can help trace back to the attacker. Which C2 server does the malware in the PPT file communicate with?

We can still manage to answer that question by doing a research on VT results. We go to the Behavior tab to see if we can find something over there. We are looking for some kind of server (url/ip) that looks like a suspicious directory. Let's scroll down a bit until and we get Memory Pattern URLs which points on urls extracted from the malware's runtime memory, not just static strings. Seems like the first url fits our needs because it points to a remote server hosting a PHP script, which is commonly used by malware (beaconing, receiving commands, downloading additional payloads). The same IP Address appears multiple times in the behavior data, indicating active communication, not an incidental reference.

<img width="1047" height="676" alt="image" src="https://github.com/user-attachments/assets/fe2ba062-6d5b-47af-aac6-93970682f5c8" />

It worked! :)

#### Q3: Identifying the initial actions of the malware post-infection can provide insights into its primary objectives. What is the first library that the malware requests post-infection?

From experience we're probably looking for a Dynamic-Link Library (that would be my first guess). These kind of files have ".ddl" extension. So we need some information about requests. We can still operate on data from VT report. Without changing tabs let's scroll up till we get HTTP Requests. There we have 3 requests (two of them are the same) 1 with a POST method and 2 with a GET method. As I said earlier - DLL files would be my first guess and there it is. We should try it!

<img width="1047" height="382" alt="image" src="https://github.com/user-attachments/assets/d47c6a64-2f1b-4dda-b4a5-4c5583c90fdc" />

It worked! :)

#### Q4: By examining the provided Any.run report, what RC4 key is used by the malware to decrypt its base64-encoded string?

This task is based on a Any.run Report and link for that report is included in the question. So let's get into this raport and we have to look for a RC4 key. A simple CTR+f would be helpful here.

<img width="940" height="311" alt="image" src="https://github.com/user-attachments/assets/af7eb57e-5af4-476b-a2da-71d0a31917ee" />

It worked! :)

#### Q5: By examining the MITRE ATT&CK techniques displayed in the Any.run sandbox report, identify the main MITRE technique (not sub-techniques) the malware uses to steal the user’s password.

Here is another link included in the question. This one is more detailed. We're looking for a technique connected to stealing the user's password. In that case we should check ATT&CK tab since it's correlated with MITRE ATT&CK techniques. Now we have a view on the MITRE Matrix and we can filter out some stuff. Let's uncheck Warnings and Others in the upper right corner. Now we have some seriuos options to consider. The case is about stealing user's password so we're interested with Credential Access techniques - that is a first step. Now we have two techniques that are correlated with this subject (Unsecured Credentials and Credentials from Password Stores), but we're asked specificaly for a technique that malware uses to steal PASSWORDS. That said I would go with the second one - Credentials from Password Stores. Remember that we only need the ID of that technique:

<img width="890" height="196" alt="image" src="https://github.com/user-attachments/assets/912e9832-5675-45ed-9bca-5bd33f704017" />

It worked! :)

#### Q6: By examining the child processes displayed in the Any.run sandbox report, which directory does the malware target for the deletion of all DLL files?

Let's get back to the main page with the detailed raport. On the right we see processes that occured during the infection. There is a cmd.exe process that may have something to do with deletion process. When we click on it there is a command line that was performed and it obviously shows that some files were deleted. After short analysis we can say that there is a directory from which all DLL files were deleted using a one command (wildcard).

<img width="818" height="272" alt="image" src="https://github.com/user-attachments/assets/1c288424-760b-4c9b-ad63-13c542d3c987" />

It worked! :)

#### Q7: Understanding the malware's behavior post-data exfiltration can give insights into its evasion techniques. By analyzing the child processes, after successfully exfiltrating the user's data, how many seconds does it take for the malware to self-delete?

This one is also simple because you can see this from the same window, just a different process ;)

<img width="807" height="287" alt="image" src="https://github.com/user-attachments/assets/850a0656-a55d-42eb-a888-5c4d3814a228" />

It worked! :)
