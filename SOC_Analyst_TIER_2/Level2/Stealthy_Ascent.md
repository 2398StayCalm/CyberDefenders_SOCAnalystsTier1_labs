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

Identify the full URL used by the attacker to download the malicious file onto the victim’s machine by examining browser artifacts from the compromised Linux image.

$${\color{red}__Analysis__}$$

The investigation focused on the victim user profile located under /home/t3m0/, where browser-related artifacts were identified inside the hidden .mozilla directory. Firefox stores browsing history and visited URLs in the places.sqlite database, making this file an important forensic artifact when reconstructing user activity or attacker-driven downloads.

The Firefox profile 644f119k.default-release contained multiple browser databases, including places.sqlite. This database was opened with DB Browser for SQLite and the moz_places table was reviewed. Most entries were normal Firefox, Mozilla, Ubuntu, and Thunderbird-related URLs, but one entry stood out because it pointed to a private IP address and referenced a shell script: update.sh.

The URL http://192.168.190.129/update.sh is suspicious because attackers commonly use simple HTTP servers hosted on internal or attacker-controlled infrastructure to deliver scripts, payloads, or post-exploitation tools. The filename update.sh also suggests an attempt to disguise the malicious script as a legitimate system update component.

$${\color{yellow}__Findings /\space IOC's__}$$

The relevant browser artifact was found at: /home/t3m0/.mozilla/firefox/644f119k.default-release/places.sqlite
The suspicious browser history entry was located in the Firefox moz_places table.
The identified download URL was: _http   :   //192.168.190.129/   update.sh_

The associated filename/title shown in browser history was: update.sh

The IP address 192.168.190.129 should be treated as attacker-controlled or malicious infrastructure within the lab environment.

$${\color{green}__Screenshot \space Analysis__}$$

<img width="1907" height="837" alt="image" src="https://github.com/user-attachments/assets/cff37350-e691-4eea-8de9-219a0bc74422" />
The first screenshot shows the contents of the victim’s Firefox profile directory under .mozilla/firefox. The presence of places.sqlite confirms that browser history data was available for review, along with other Firefox artifacts such as cookies, permissions, and extension data.

<img width="1375" height="522" alt="image" src="https://github.com/user-attachments/assets/3b95ebb7-119d-494b-8f51-6698e2a9dfbb" />
The second screenshot shows the victim user’s home directory, including hidden folders such as .mozilla, .thunderbird, .ssh, and other configuration directories. This confirms that the analysis was focused on the correct user profile and that browser artifacts were present in the compromised account.

<img width="1917" height="517" alt="image" src="https://github.com/user-attachments/assets/c3182245-6a72-4b35-b4f9-948e962b6a75" />
The third screenshot shows places.sqlite opened in DB Browser for SQLite. In the moz_places table, the suspicious entry http://192.168.190.129/update.sh is visible and selected, confirming the full URL used to retrieve the malicious file.

Answer: **http   :   //192.168.190.129/   update.sh**

It worked! 🔥

#### ❔  4: What is the full file path where this malicious persistence service was created?

$${\color{purple}__Objective__}$$

Identify the full file path where the attacker created a malicious persistence service on the compromised Linux server.

$${\color{red}__Analysis__}$$

The investigation focused on command history from the victim user account because .bash_history can reveal attacker activity, executed commands, file creation attempts, and persistence configuration steps. Reviewing shell history is especially useful in this scenario because the attacker appears to have interacted directly with the system after gaining access.

The .bash_history contents show several suspicious commands, including execution of ransomware.sh, use of update.sh, SUID privilege escalation activity, and interaction with system-level locations. One command is particularly relevant to persistence: sudo nano /etc/systemd/system/persistence.service.

This command indicates that the attacker used nano with elevated privileges to create or modify a systemd service file. Files placed under /etc/systemd/system/ are commonly used to define services that can start automatically at boot or be triggered by systemd, making this a strong persistence indicator.

$${\color{yellow}__Findings /\space IOC's__}$$

The malicious persistence service path identified in shell history was: /etc/systemd/system/persistence.service
The command used to create or edit the service was:_sudo nano /etc/systemd/system/persistence.service_

Additional suspicious activity visible in the same history includes execution of ransomware.sh, execution of update.sh, creation and execution of a SUID-related binary named suid_shell, and commands associated with disk mounting and filesystem manipulation.
The presence of a systemd service file strongly suggests the attacker attempted to maintain access or execute malicious code automatically after reboot.

$${\color{green}__Screenshot \space Analysis__}$$

<img width="1197" height="456" alt="image" src="https://github.com/user-attachments/assets/9b395432-a08f-47f0-aa33-f42d3866e1e2" />
The screenshot shows the contents of the victim user’s .bash_history file. Multiple attacker-like commands are visible, including privilege escalation attempts and script execution, which helps reconstruct the attacker’s activity timeline.

The highlighted command sudo nano /etc/systemd/system/persistence.service directly identifies where the malicious persistence service was created. Because the path is under the systemd service directory and was edited with sudo, it indicates the attacker had or obtained elevated privileges when configuring persistence.

Answer: **/etc/systemd/system/persistence.service**

It worked! 🔥

#### ❔  5: What is the path of the persistence file that is created or modified to maintain the attacker's access?

$${\color{purple}__Objective__}$$

Determine which file or script was configured by the malicious systemd service to maintain the attacker’s access to the compromised Linux server.

$${\color{red}__Analysis__}$$

The investigation continued from the previously identified persistence service located at /etc/systemd/system/persistence.service. After confirming that the service file existed in the systemd service directory, its contents were reviewed to determine what payload or script it executed.

The service file contains a standard systemd unit structure with [Unit], [Service], and [Install] sections. The key evidence is located in the ExecStart directive, which defines the command systemd runs when the service starts. In this case, the service is configured to execute /bin/bash /tmp/P3r515t3nc3.sh.

This indicates that the attacker used the systemd service as a launcher for a script stored in /tmp. The filename _P3r515t3nc3.sh_ is a leetspeak variation of “persistence,” strongly suggesting intentional malicious use. Because /tmp is writable and often abused by attackers for staging scripts, this file should be treated as a persistence payload or access-maintenance script.

$${\color{yellow}__Findings /\space IOC's__}$$

The malicious service file reviewed was: /etc/systemd/system/persistence.service
The service description was: Persistence Service
The persistence payload referenced by the service was: _/tmp/P3r515t3nc3.sh_

The execution command configured in the service was: ExecStart=/bin/bash _/tmp/P3r515t3nc3.sh_
The service was configured with: Restart=always
The service was also tied to: WantedBy=multi-user.target

These settings indicate that the malicious script was intended to run under systemd and restart automatically if stopped, increasing the attacker’s ability to maintain access.

$${\color{green}__Screenshot \space Analysis__}$$

The screenshot shows the contents of /etc/systemd/system/, where persistence.service is present among legitimate systemd service files. Its presence in this directory confirms that the attacker placed the persistence mechanism in a location used by Linux to manage system services.
<img width="1855" height="622" alt="image" src="https://github.com/user-attachments/assets/546f9a79-2e40-4688-8384-97b548af0c56" />

The command output also confirms that persistence.service is an ASCII text file and then displays its configuration. The ExecStart=/bin/bash /tmp/P3r515t3nc3.sh line identifies the script executed by the service, while Restart=always shows that the attacker attempted to keep the payload running persistently.

Answer: **/tmp/P3r515t3nc3.sh**

It worked! 🔥

#### ❔  6: During the browser's security check, it identified potentially harmful activity. Which files related to safe browsing were flagged, specifically those with the .vlpset extension?

$${\color{purple}__Objective__}$$

Identify the Firefox Safe Browsing .vlpset files that were flagged during the browser’s security checks and determine which specific entries were relevant to potentially harmful activity.

$${\color{red}__Analysis__}$$

The investigation focused on the victim user’s home directory, specifically the Firefox cache and Safe Browsing data stored under the .cache/mozilla/firefox/ path. Firefox maintains Safe Browsing files to help detect and block phishing pages, malware, unwanted software, and other suspicious web content. Files with the .vlpset extension are part of this Safe Browsing dataset.

A filesystem search was performed for all files matching the *.vlpset extension. The results showed multiple Safe Browsing-related files, including tracking protection, cryptomining, fingerprinting, and Google threat intelligence datasets. The most relevant entries were located under the safebrowsing/google4/ directory, which contained files associated with phishing, malware, unwanted software, bad binary reputation, and download whitelist checks.

The identified google4 .vlpset files are significant because they represent browser security datasets used to classify or validate potentially harmful browser activity. In the context of the earlier malicious download URL, these files support that Firefox’s Safe Browsing components were present and potentially involved in evaluating risky content.

$${\color{yellow}__Findings /\space IOC's__}$$

The Safe Browsing .vlpset files identified under the Firefox cache were:
- _goog-phish-proto.vlpset_
- _goog-badbinurl-proto.vlpset_
- _goog-malware-proto.vlpset_
- _goog-unwanted-proto.vlpset_
- _goog-downloadwhite-proto.vlpset_

The relevant artifact directory was:
/home/t3m0/.cache/mozilla/firefox/644f119k.default-release/safebrowsing/google4/

These files are related to browser-based detection of phishing, malware, unwanted software, suspicious downloads, and binary reputation checks.

$${\color{green}__Screenshot \space Analysis__}$$

The first screenshot shows a find command searching the victim user’s home directory for files ending in .vlpset. The output lists numerous Firefox Safe Browsing files located under the Mozilla cache path, confirming that browser security datasets were present on the system.
<img width="1307" height="450" alt="image" src="https://github.com/user-attachments/assets/aa5825d3-5602-45b6-b4ee-6dda45d68c28" />

The second screenshot shows the selected Safe Browsing files formatted as the final set of relevant answers. The highlighted entries are all located in the Google Safe Browsing google4 category and are specifically associated with phishing, malware, unwanted software, bad binary URLs, and download whitelist checks.
<img width="1827" height="157" alt="image" src="https://github.com/user-attachments/assets/db5cfa18-ca68-4a1a-bdd9-1ae39bb0727c" />

Answer: **goog-phish-proto.vlpset, goog-badbinurl-proto.vlpset, goog-malware-proto.vlpset, goog-unwanted-proto.vlpset, goog-downloadwhite-proto.vlpset**

It worked! 🔥

#### ❔  7: When did the attacker successfully connect to the victim for the first time?

$${\color{purple}__Objective__}$$

Determine the timestamp of the attacker’s first successful SSH connection to the victim machine by reviewing authentication logs from the compromised Linux server.

$${\color{red}__Analysis__}$$

The investigation focused on /var/log/auth.log, which records authentication events such as sudo usage, SSH service activity, login attempts, and session openings. Since the attacker appears to have enabled or used SSH for access, filtering this log for ssh events was an appropriate way to identify successful remote connections.

The log shows activity related to installing and enabling OpenSSH server, followed by SSH daemon startup messages. Shortly afterward, the first successful SSH authentication event appears. The relevant entry shows an accepted password login for user t3m0 from IP address 192.168.190.129 over SSH.

This event is important because it confirms not only when the attacker first authenticated successfully, but also the source IP address used for access. The same IP address was previously associated with the malicious download URL, strengthening its attribution as attacker-controlled infrastructure within the lab.

$${\color{yellow}__Findings /\space IOC's__}$$

The authentication log reviewed was: /var/log/auth.log
The first successful SSH login event was: _Jul 30 13:51_ ubuntu sshd[11095]: Accepted password for t3m0 from 192.168.190.129 port 50156 ssh2

The affected user account was: t3m0
The source IP address was: 192.168.190.129
The connection used SSH protocol version: ssh2

The log also shows SSH service setup activity shortly before the login, including OpenSSH server installation, SSH service start, and SSH service enablement.

$${\color{green}__Screenshot \space Analysis__}$$

The screenshot shows auth.log being searched for SSH-related activity. The output includes OpenSSH installation and service startup events, followed by successful login records.

The highlighted entry is the first accepted SSH password authentication for user t3m0 from 192.168.190.129. This confirms the attacker’s first successful connection time as _Jul 30 13:51_.
<img width="1511" height="427" alt="image" src="https://github.com/user-attachments/assets/a6e171cf-65e3-4c01-89e4-25dffdec4933" />

Answer: **Jul 30 13:51**

It worked! 🔥

#### ❔  8: What is the name of the file that is responsible for encrypting folders with the specific extension?

$${\color{purple}__Objective__}$$

Identify the file responsible for encrypting folders with the specified extension by reviewing suspicious user artifacts on the compromised Linux server.

$${\color{red}__Analysis__}$$

The investigation focused on the t3m0 user profile because previous evidence showed that attacker activity and relevant artifacts were associated with this account. Hidden directories were reviewed first, including application and local user data paths, since attackers often stage or hide tools in user-writable locations.

During the review of /home/t3m0/.local/share/, the Trash directory was inspected. This location can contain deleted or intentionally hidden attacker tools, especially if the attacker attempted to remove evidence after execution. Inside the Trash structure, the files directory contained a suspicious shell script named ransomware.sh.

The filename is directly associated with ransomware behavior and matches earlier shell history evidence showing that _ransomware.sh_ had been made executable and run. This strongly indicates that the script was responsible for encrypting folders or applying the specific malicious extension referenced in the lab scenario.

$${\color{yellow}__Findings /\space IOC's__}$$

The suspicious file identified was: _ransomware.sh_
The full artifact path was: /home/t3m0/.local/share/Trash/files/ransomware.sh
The file was found inside the user’s Trash directory, suggesting it may have been deleted after execution or intentionally moved there to hide evidence.

This finding also correlates with prior .bash_history evidence showing commands such as: 
- chmod +x ransomware.sh
- ./ransomware.sh

These commands indicate that the script was prepared for execution and then run on the victim machine.

$${\color{green}__Screenshot \space Analysis__}$$

The screenshot shows manual navigation through the t3m0 user’s home directory and hidden folders. The analyst reviews .thunderbird, then moves into .local/share/Trash, which is a relevant location for deleted or concealed user-level artifacts.
<img width="1669" height="440" alt="image" src="https://github.com/user-attachments/assets/9b6677d9-2579-43d3-bb69-cefb61254c06" />

Inside .local/share/Trash/files/, the file _ransomware.sh_ is visible and highlighted. Its name and location make it a strong indicator of the script used to perform encryption activity on the compromised system.

Answer: **ransomware.sh**

It worked! 🔥

#### ❔  9: What is the original file path of the program that encrypts folders?

$${\color{purple}__Objective__}$$

Determine the original full file path of the program responsible for encrypting folders on the compromised Linux server.

$${\color{red}__Analysis__}$$

The investigation continued from the previously identified ransomware-related file, ransomware.sh. Since the file was discovered inside the victim user’s Trash directory, the next step was to confirm its exact filesystem location rather than relying only on the directory listing.

The analyst navigated into the files directory under the user’s Trash path and used pwd to print the current working directory. This confirmed the full directory path where the encryption program was located. Combining the confirmed directory path with the listed filename provides the complete original file path of the ransomware script.

The location is significant because /home/t3m0/.local/share/Trash/files/ stores files that were deleted through the desktop environment. This suggests the ransomware script may have been removed after execution, either by the attacker to hide evidence or as part of cleanup activity.

$${\color{yellow}__Findings /\space IOC's__}$$

The encryption program identified was: ransomware.sh
The confirmed directory path was: /mnt/lab/home/t3m0/.local/share/Trash/files
The full file path was: _/mnt/lab/home/t3m0/.local/share/Trash/files/ransomware.sh_

This file should be treated as a malicious script associated with encryption activity. Its placement in the Trash directory indicates likely deletion or attempted concealment after use.

$${\color{green}__Screenshot \space Analysis__}$$

The screenshot shows the analyst inside /mnt/lab/home/t3m0/.local/share/Trash/files and running pwd && ls. This confirms both the exact directory path and the presence of ransomware.sh in that location.
<img width="855" height="112" alt="image" src="https://github.com/user-attachments/assets/5dae9329-f34e-457b-98ff-c2d138726cdb" />

The command output provides direct evidence of the ransomware script’s full path. Because the mounted image is accessed under /mnt/lab, the observed forensic path includes that mount point.

Answer: **/mnt/lab/home/t3m0/.local/share/Trash/files/ransomware.sh**

It worked! 🔥

#### ❔  10: What is the encryption key the attacker used to encrypt the files?

$${\color{purple}__Objective__}$$

Identify the encryption key used by the attacker by reviewing the contents of the ransomware script discovered in the victim user’s Trash directory.

$${\color{red}__Analysis__}$$

After identifying ransomware.sh as the file responsible for encrypting folders, the next step was to inspect its contents. Reviewing the script source was necessary to understand how the encryption routine worked, which directory was targeted, and whether any hardcoded key or configuration value was present.

The script targets /home/t3m0/Downloads and creates a ransom note named README_FOR_DECRYPTION.txt. It then searches for files in the target directory and encrypts them using openssl enc -aes-256-cbc -salt. The encrypted output is written with a .enc extension, and the original file is removed after successful encryption.

The encryption key is hardcoded in the script as the value of the ENCRYPTION_KEY variable. This confirms the exact key used by the attacker to encrypt the victim’s files.

$${\color{yellow}__Findings /\space IOC's__}$$

The malicious script reviewed was: /mnt/lab/home/t3m0/.local/share/Trash/files/ransomware.sh
The target directory configured in the script was: /home/t3m0/Downloads
The ransom note created by the script was: README_FOR_DECRYPTION.txt

The encryption method used was: openssl enc -aes-256-cbc -salt
The encrypted files were written with the extension: .enc
The hardcoded encryption key was: _s3cr3t_k3y_

$${\color{green}__Screenshot \space Analysis__}$$

The screenshot shows the contents of ransomware.sh being displayed with cat. The script clearly defines the target directory, the encryption key variable, the ransom note creation command, and the OpenSSL encryption routine.
<img width="1462" height="477" alt="image" src="https://github.com/user-attachments/assets/2fa24b18-63c5-4998-a89c-38d11fd66055" />

The highlighted line shows _ENCRYPTION_KEY="s3cr3t_k3y"_, which directly identifies the key used for encryption. This value is the critical recovery-related artifact for understanding and potentially reversing the ransomware activity in the lab.

Answer: **s3cr3t_k3y**

It worked! 🔥

#### ❔  11: The attacker extracted passwords from the victim and stored them in a file. What is the name of this file?

$${\color{purple}__Objective__}$$

Identify the file where the attacker stored extracted password data from the victim system.

$${\color{red}__Analysis__}$$

The investigation returned to the t3m0 user’s .bash_history because previous findings showed that the attacker’s actions were recorded there. Shell history is especially useful for identifying post-exploitation activity, including credential access, privilege escalation, and data staging.

The highlighted command shows the attacker running unshadow against /etc/passwd and /etc/shadow. This technique combines Linux account information with password hash data, typically preparing it for offline password cracking. The output was redirected into a file, which reveals where the extracted password material was stored.

The command sudo unshadow /etc/passwd /etc/shadow > _unshadowed.txt_ confirms that the attacker saved the extracted credential hash data into unshadowed.txt.

$${\color{yellow}__Findings /\space IOC's__}$$

The credential extraction command identified was: sudo unshadow /etc/passwd /etc/shadow > _unshadowed.txt_
The source files used for extraction were: 
- /etc/passwd
- /etc/shadow

The output file containing the extracted password data was: _unshadowed.txt_

This file should be treated as a sensitive credential artifact because it likely contains combined account and password hash data suitable for offline cracking.

$${\color{green}__Screenshot \space Analysis__}$$

The screenshot shows the contents of the t3m0 user’s .bash_history file. The first highlighted command captures the use of unshadow, which is commonly used to combine /etc/passwd and /etc/shadow entries for password cracking workflows.
<img width="895" height="352" alt="image" src="https://github.com/user-attachments/assets/292e64c2-2da6-4f6c-af2a-019bf3e9805a" />

The output redirection operator > shows that the results were saved into unshadowed.txt. This directly identifies the file where the attacker stored the extracted password data.

Answer: **unshadowed.txt**

It worked! 🔥

#### ❔  12: What is the user ID and the command included in the script that the attacker used to spawn a shell when the file was encrypted?

$${\color{purple}__Objective__}$$

Identify the user ID and shell-spawning command contained in the encrypted script used by the attacker.

$${\color{red}__Analysis__}$$

The investigation focused on the encrypted file suid_shell.c.enc, which was located in the victim user’s Downloads directory. Since previous analysis identified the ransomware encryption key, the file was decrypted using openssl with AES-256-CBC. The decrypted output was saved as suid_shell.c.

After decryption, the file type confirmed that suid_shell.c was a C source code file. The file contents were then reviewed with sed, revealing a small program designed to change its effective user ID and launch a shell. The code contains setuid(0);, which attempts to set the process user ID to _0_, the root user. It then executes _system("/bin/sh")_;, which spawns a shell.

This behavior is consistent with a privilege escalation helper. If compiled and assigned the SUID bit as root, the binary could allow the attacker to spawn a root shell.

$${\color{yellow}__Findings /\space IOC's__}$$

The encrypted file reviewed was: suid_shell.c.enc
The decrypted source file was: suid_shell.c
The relevant directory was: /mnt/lab/home/t3m0/Downloads/
The user ID used in the script was: 0
The shell-spawning command was: system("/bin/sh");
**The relevant source code lines were:**
- setuid(0);
- system("/bin/sh");

This code indicates an attempt to spawn a shell with root privileges, assuming the compiled binary was executed with the appropriate SUID permissions.

$${\color{green}__Screenshot \space Analysis__}$$

The screenshot shows suid_shell.c.enc being decrypted with openssl and written to suid_shell.c. The successful file check confirms that the decrypted output is a C source file rather than random or corrupted data.
<img width="1740" height="437" alt="image" src="https://github.com/user-attachments/assets/3a288ae8-f7b6-404f-a05c-39ee3374897f" />

The displayed source code shows a minimal C program that calls setuid(0) and then runs /bin/sh. These lines directly identify both the user ID and the command used by the attacker to spawn a shell.

Answer: _setuid(0) and system("/bin/sh")_ ***0,/bin/sh*;

It worked! 🔥

All done! 🥇
