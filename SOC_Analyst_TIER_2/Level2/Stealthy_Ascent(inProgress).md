# 💻 Stealthy Ascent
📌Link to the lab -> [https://cyberdefenders.org/blueteam-ctf-challenges/stealthy-ascent/]

### 📓 Scenario:
As a cybersecurity analyst at Defenders Solutions, you have been alerted to suspicious activity on one of the company's critical Linux servers. Initial reports indicate attackers may have exploited a hidden backdoor to gain root privileges.

Your task is to thoroughly investigate the server to uncover the attacker's methods of privilege escalation and persistence. Your analysis will be essential in identifying the extent of the compromise and securing the system against future attacks.

### ❓Questions❓:

#### ❔  1: What is the attacker's email address?

$${\color{purple}__Objective__}$$

Identify the attacker’s email address by examining the provided compromised Linux server image and reviewing user-level artifacts that may contain attacker communications.

$${\color{red}__Analysis__}$$

The investigation began by reviewing the lab instructions in the Start here directory. The briefing confirmed that the primary artifact for analysis was a compromised Linux server image named LinuxServer.img.

The disk image was mounted as a loop device and accessed through /mnt/lab, allowing the filesystem to be reviewed directly. After confirming the presence of a standard Linux directory structure, the investigation focused on user directories and hidden application data, as these locations often contain cached credentials, browser data, mailboxes, or attacker communication traces.

Inside /home/t3m0/, a hidden Thunderbird profile was discovered. Thunderbird is an email client, so its local profile data was a strong candidate for finding relevant communication artifacts. Within the profile path, the ImapMail/imap.gmail.com directory contained an INBOX mailbox file. Header searches against this mailbox revealed several email addresses, including legitimate Google senders and one suspicious sender using a security-themed alias.

$${\color{yellow}__Findings /\space IOC's__}$$

| Type          | Indicator                 |Location / Context                                                                                   | Assessment                               |
| ------------- | --------------------------|-----------------------------------------------------------------------------------------------------|------------------------------------------| 
| Email Address | `info.s3c1337@gmail.com`  | Found in Thunderbird `INBOX` message headers as `From: info sec <info   .    s3c1337@gmail.   com>` | Likely attacker-controlled email address |
| Email Address | `atammam371@gmail.com`    | Found in Thunderbird mailbox headers as the recipient account                                       | Victim/user mailbox account              |
| File Path     | `/home/t3m0/.thunderbird/`| Hidden Thunderbird profile directory                                                                | User email artifact location             |
| Disk Image    | `LinuxServer.img`         | Provided lab artifact                                                                               | Compromised Linux server image           |


$${\color{green}__Screenshot \space Analysis__}$$

The first screenshot shows the lab briefing from the Start here directory. It establishes the investigation scope: a compromised Linux server where the analyst must identify attacker activity, privilege escalation, and persistence.
<img width="1871" height="762" alt="image" src="https://github.com/user-attachments/assets/85e1d54a-e222-4b77-8a1a-7a293b5d70af" />

The second screenshot shows LinuxServer.img being attached as a loop device and mounted to /mnt/lab. This confirms that the analyst accessed the disk image safely as evidence rather than relying on the live compromised system.
<img width="1907" height="595" alt="image" src="https://github.com/user-attachments/assets/072737bb-9b2a-4888-b5e4-28ab52c241b7" />

The third screenshot confirms successful access to the mounted Linux filesystem. Standard directories such as /etc, /home, /root, /tmp, and /var are visible, providing starting points for artifact review.
<img width="1131" height="696" alt="image" src="https://github.com/user-attachments/assets/eace9102-0409-429e-a4e5-85bd79e72657" />

The fourth screenshot shows navigation into the hidden Thunderbird profile under /home/t3m0/. The presence of imap.gmail.com, INBOX, and Thunderbird mailbox metadata files indicates that cached email content is available for review.
<img width="1391" height="522" alt="image" src="https://github.com/user-attachments/assets/4f09eed2-7803-4f35-85cd-7d03737273db" />

The final screenshot shows mailbox header extraction from the Thunderbird INBOX file. Several expected Google-related messages are present, but info sec <_info   .   s3c1337@gmail   .   com_> stands out as suspicious and is identified as the attacker email address.
<img width="1752" height="337" alt="image" src="https://github.com/user-attachments/assets/962e6122-2e19-46bc-9e52-fcea439be6f5" />

Answer: _info   .    s3c1337@gmail   .   com_

It worked! 🔥

#### ❔  2: What is the name of the attachment the attacker sent to the victim?

$${\color{purple}__Objective__}$$

Determine the name of the attachment sent by the attacker to the victim by reviewing the suspicious email previously identified in the Thunderbird mailbox.

$${\color{red}__Analysis__}$$

After identifying info    .    s3c1337@gmail   .   com as the suspicious sender, the next step was to inspect the full contents of the related message inside the Thunderbird INBOX file. The email subject, Urgent Security, and the wording of the message indicate a social engineering attempt. The sender instructed the victim to open an attached document and follow the included steps immediately, which is consistent with phishing or initial compromise activity.

The mailbox content showed that the message was multipart MIME formatted, meaning it contained multiple sections such as plaintext, HTML, and potentially file attachments. Searching the message content for attachment-related metadata revealed a Content-Disposition: attachment field. This field included the filename of the document delivered by the attacker.

$${\color{yellow}__Findings /\space IOC's__}$$

The suspicious email was sent from info   .   s3c1337@gmail   .   com to atammam371@gmail.com.
The message subject was Urgent Security, suggesting an attempt to pressure the victim into opening the attachment.
The email body instructed the victim to review an attached document containing steps to “secure your system,” which is a common pretext used in phishing campaigns.
The attachment metadata was found in the Thunderbird mailbox file at: _/home/t3m0/.thunderbird/lilc5p7e.default-release/ImapMail/imap.gmail.com/INBOX_
The attachment filename identified in the message was: **_Important.docx_**

$${\color{green}__Screenshot \space Analysis__}$$

The first screenshot shows the suspicious email content being extracted from the Thunderbird INBOX file using grep. The message headers confirm that it was sent by info sec <info   .   s3c1337@gmail  .  com> and included the subject Urgent Security, followed by language encouraging the victim to open the attached document.
<img width="1507" height="557" alt="image" src="https://github.com/user-attachments/assets/47d55484-f2fc-4416-9089-8f8b345adaf6" />

The second screenshot shows a targeted search for attachment metadata within the same email content. The result displays Content-Disposition: attachment; filename="_Important.docx_", confirming the exact name of the file sent by the attacker.
<img width="1696" height="82" alt="image" src="https://github.com/user-attachments/assets/b12cc930-82a9-482b-a20f-4f978098c117" />

Answer: **_Important.docx_**

It worked! 🔥

#### ❔  3: What is the full URL the attacker used to download the malicious file on the victim's machine?

$${\color{purple}__Objective__}$$
$${\color{red}__Analysis__}$$
$${\color{yellow}__Findings /\space IOC's__}$$
$${\color{green}__Screenshot \space Analysis__}$$

#### ❔  4: What is the full file path where this malicious persistence service was created?

$${\color{purple}__Objective__}$$
$${\color{red}__Analysis__}$$
$${\color{yellow}__Findings /\space IOC's__}$$
$${\color{green}__Screenshot \space Analysis__}$$

#### ❔  5: What is the path of the persistence file that is created or modified to maintain the attacker's access?

$${\color{purple}__Objective__}$$
$${\color{red}__Analysis__}$$
$${\color{yellow}__Findings /\space IOC's__}$$
$${\color{green}__Screenshot \space Analysis__}$$

#### ❔  6: During the browser's security check, it identified potentially harmful activity. Which files related to safe browsing were flagged, specifically those with the .vlpset extension?

$${\color{purple}__Objective__}$$
$${\color{red}__Analysis__}$$
$${\color{yellow}__Findings /\space IOC's__}$$
$${\color{green}__Screenshot \space Analysis__}$$

#### ❔  7: When did the attacker successfully connect to the victim for the first time?

$${\color{purple}__Objective__}$$
$${\color{red}__Analysis__}$$
$${\color{yellow}__Findings /\space IOC's__}$$
$${\color{green}__Screenshot \space Analysis__}$$

#### ❔  8: What is the name of the file that is responsible for encrypting folders with the specific extension?

$${\color{purple}__Objective__}$$
$${\color{red}__Analysis__}$$
$${\color{yellow}__Findings /\space IOC's__}$$
$${\color{green}__Screenshot \space Analysis__}$$

#### ❔  9: What is the original file path of the program that encrypts folders?

$${\color{purple}__Objective__}$$
$${\color{red}__Analysis__}$$
$${\color{yellow}__Findings /\space IOC's__}$$
$${\color{green}__Screenshot \space Analysis__}$$

#### ❔  10: What is the encryption key the attacker used to encrypt the files?

$${\color{purple}__Objective__}$$
$${\color{red}__Analysis__}$$
$${\color{yellow}__Findings /\space IOC's__}$$
$${\color{green}__Screenshot \space Analysis__}$$

#### ❔  11: The attacker extracted passwords from the victim and stored them in a file. What is the name of this file?

$${\color{purple}__Objective__}$$
$${\color{red}__Analysis__}$$
$${\color{yellow}__Findings /\space IOC's__}$$
$${\color{green}__Screenshot \space Analysis__}$$

#### ❔  12: What is the user ID and the command included in the script that the attacker used to spawn a shell when the file was encrypted?

$${\color{purple}__Objective__}$$
$${\color{red}__Analysis__}$$
$${\color{yellow}__Findings /\space IOC's__}$$
$${\color{green}__Screenshot \space Analysis__}$$

