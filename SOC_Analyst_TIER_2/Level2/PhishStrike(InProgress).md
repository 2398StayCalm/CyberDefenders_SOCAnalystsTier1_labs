# 💻 PhishStrike  
📌Link to the lab -> [https://cyberdefenders.org/blueteam-ctf-challenges/phishstrike/]

### 📓 Scenario:
As a cybersecurity analyst at an educational institution, you receive an alert about a phishing email targeting faculty members. The email appears to be from a trusted contact and claims a $625,000 purchase, providing a link to download an invoice.
Your task is to investigate the email using Threat Intel tools. Analyze the email headers and inspect the link for malicious content. Identify any Indicators of Compromise (IOCs) and document your findings to prevent potential fraud and educate faculty on phishing recognition.

### ❓Questions❓:

#### ❔  1: Identifying the sender's IP address with specific SPF and DKIM values helps trace the source of the phishing email. What is the sender's IP address that has an SPF value of softfail and a DKIM value of fail?

$${\color{purple}__Objective__}$$

Analyze the phishing email headers to identify the originating sender IP address and evaluate the message’s email-authentication results. The investigation focuses on SPF, DKIM, and DMARC indicators that may reveal sender spoofing or unauthorized use of a trusted domain.

$${\color{red}__Analysis__}$$

The email header was submitted to the MXToolbox Email Header Analyzer for inspection. The analyzed message used the subject “COMMERCIAL PURCHASE RECEIPT ONLINE 27 NOV,” which is consistent with the financial lure described in the scenario.

The authentication results show that the message was not successfully validated. SPF returned softfail, meaning the sending IP was not clearly authorized by the purported sender domain’s SPF policy. DKIM returned fail, indicating that the message did not contain a valid cryptographic signature for the claimed domain. The analyzer also reported the message as not DMARC compliant, further reducing confidence in the sender’s legitimacy.

Within the ARC-Authentication-Results header, the IP associated with the failed authentication results is _18.208.22.104_. These combined indicators strongly suggest that the message may have been spoofed or sent through unauthorized infrastructure.

$${\color{yellow}__Findings /\space IOC's__}$$

| Indicator     | Value                                         | Assessment                                          |
| ------------- | --------------------------------------------- | --------------------------------------------------- |
| Sender IP     | **18.208.22.104**                             | IP associated with the suspicious message           |
| SPF result    | **softfail**                                  | Sender not clearly authorized                       |
| DKIM result   | **fail**                                      | Message signature validation failed                 |
| DMARC result  | **Not compliant**                             | Sender identity could not be reliably authenticated |
| Email subject | **COMMERCIAL PURCHASE RECEIPT ONLINE 27 NOV** | Financial phishing lure                             |

$${\color{green}__Screenshot \space Analysis__}$$

The first screenshot shows the email header analysis results in MXToolbox. Although SPF and DKIM alignment are displayed, both authentication checks fail, and the message is marked as not DMARC compliant, indicating that domain alignment alone did not establish sender authenticity.
<img width="1882" height="760" alt="image" src="https://github.com/user-attachments/assets/88c2fe15-3ac1-4475-81d3-7c276b5cb0ee" />

The second screenshot provides the relevant raw header evidence. The ARC-Authentication-Results field identifies _18.208.22.104_ as the sender IP and records spf=softfail and dkim=fail, directly answering the investigation question and supporting classification of the email as suspicious.
<img width="1192" height="420" alt="image" src="https://github.com/user-attachments/assets/76d565ae-6e6a-4677-91f7-c9c13db1e2ab" />

Answer: **18.208.22.104**

I worked! 🔥

#### ❔  2: Understanding the return path of an email is essential for tracing its origin. What is the return path specified in this email?

$${\color{purple}__Objective__}$$

Identify the email’s Return-Path value to determine the address designated to receive delivery failures and bounce notifications. This field can help trace the sending infrastructure and reveal inconsistencies between the visible sender and the underlying envelope sender.

$${\color{red}__Analysis__}$$

The analyzed email headers contain a Return-Path field with the value erikajohana.lopez@uptc.edu.co. The Return-Path is typically added during message delivery and represents the SMTP envelope sender rather than necessarily matching the address displayed in the email’s From field.

In a phishing investigation, this value should be compared with the visible sender, sending domain, SPF result, and other authentication records. Although the address uses the uptc.edu.co domain, the earlier SPF softfail and DKIM failure indicate that the message was not successfully authenticated, so the Return-Path alone should not be treated as proof of legitimacy.

$${\color{yellow}__Findings /\space IOC's__}$$

| Indicator              | Value                                | Assessment                                               |
| ---------------------- | ------------------------------------ | -------------------------------------------------------- |
| Return-Path            | **[erikajohana.lopez@uptc.edu.co]**  | Envelope sender associated with the message              |
| Return-Path domain     | **uptc.edu.co**                      | Should be correlated with SPF, DKIM, and sender identity |
| Authentication context | **SPF softfail / DKIM fail**         | Reduces trust in the claimed sender domain               |


$${\color{green}__Screenshot \space Analysis__}$$

The first screenshot shows the Headers Found section of the email-analysis tool, where individual header names and their corresponding values are listed for review. This section provides the analyst with a structured view of the message metadata.
<img width="1777" height="222" alt="image" src="https://github.com/user-attachments/assets/a2019fd5-8a6c-4dc4-84ad-ee60937720d9" />

The second screenshot isolates the Return-Path entry and shows the value _erikajohana.lopez@uptc.edu.co_. This is the required answer and identifies the SMTP envelope sender recorded in the email headers.
<img width="755" height="42" alt="image" src="https://github.com/user-attachments/assets/2c3ded9f-4ff0-45a5-b5c9-bb61a52979ec" />

Answer: **erikajohana.lopez@uptc.edu.co**

It worked! 🔥

#### ❔  3: Identifying the source of malware is critical for effective threat mitigation and response. What is the IP address of the server hosting the malicious file related to malware distribution?

$${\color{purple}__Objective__}$$

Identify the server hosting the malicious file referenced in the phishing email and validate whether the associated infrastructure has been linked to malicious activity.

$${\color{red}__Analysis__}$$

Inspection of the email body revealed a direct download link presented as an invoice:

http   :   //107.175.247.199   /loader/install.exe

The URL uses a raw IP address rather than a recognizable business domain and points directly to an executable file named install.exe. This is highly suspicious because legitimate invoices are normally delivered as documents or accessed through authenticated business portals, not downloaded as Windows executables.

The hosting IP, _107.175.247.199_, was then checked in VirusTotal. The screenshot shows that 9 of 91 security vendors flagged the IP as malicious, with a negative community score. This corroborates the email-content analysis and indicates that the server may be associated with malware distribution or other hostile activity.

$${\color{yellow}__Findings /\space IOC's__}$$

| Indicator            | Value                                             | Assessment                                          |
| -------------------- | ------------------------------------------------- | --------------------------------------------------- |
| Malicious hosting IP | **107.175.247.199**                               | Server hosting the suspected malware                |
| Malicious URL        | `http   ://107.175.247.199   /loader/install.exe` | Direct executable download                          |
| File name            | `install.exe`                                     | Likely malware payload disguised as invoice content |
| VirusTotal result    | **9/91 detections**                               | Multiple vendors classify the IP as malicious       |
| Network owner        | **AS36352 / HostPapa**                            | Hosting context shown in VirusTotal                 |


$${\color{green}__Screenshot \space Analysis__}$$

The first screenshot shows the decoded email body and exposes the link embedded beneath the invoice prompt. The URL resolves directly to _107.175.247.199_ and requests an executable from the /loader/ directory, which strongly indicates an attempt to deliver malware rather than an invoice.
<img width="1817" height="507" alt="image" src="https://github.com/user-attachments/assets/b1f13345-54b4-4f64-aea3-a0924aac8f1d" />

The second screenshot shows the VirusTotal reputation results for _107.175.247.199_. Nine security vendors flagged the address as malicious, supporting the conclusion that the IP is the malware-hosting server and should be blocked and monitored across institutional security controls.
<img width="1880" height="287" alt="image" src="https://github.com/user-attachments/assets/4798d23b-0948-4ba0-916a-be8e83a7a6e3" />

Answer: **107.175.247.199**

It worked! 🔥

#### ❔  4: Identifying malware that exploits system resources for cryptocurrency mining is critical for prioritizing threat mitigation efforts. The malicious URL can deliver several malware types. Which malware family is responsible for cryptocurrency mining?

$${\color{purple}__Objective__}$$

Determine which malware classification associated with the malicious invoice URL is responsible for unauthorized cryptocurrency mining.

$${\color{red}__Analysis__}$$

The previously identified URL, http:   //107.175.247.199   /loader/install.exe, was investigated in URLhaus. The record classifies the resource as a malware download and associates it with multiple tags: AsyncRAT, bitrat, and _CoinMiner_.

Among these classifications, CoinMiner is the malware type linked to cryptocurrency mining. Coin-mining malware abuses a compromised system’s CPU or GPU resources to generate cryptocurrency for an attacker. In an educational environment, this can cause degraded device performance, increased power consumption, instability, and may also indicate that the host has been fully compromised.

The URL was shown as offline at the time of the recorded lookup, but the entry remains relevant as historical threat intelligence. An offline status does not make previously exposed systems safe; endpoints that accessed the URL should still be reviewed for execution evidence and persistence.

$${\color{yellow}__Findings /\space IOC's__}$$

| Indicator                     | Value                                             | Assessment                                            |
| ----------------------------- | --------------------------------------------------| ----------------------------------------------------- |
| Cryptocurrency-mining malware | **CoinMiner**                                     | Abuses system resources to mine cryptocurrency        |
| Malicious URL                 | `http   ://107.175.247.199/   loader/install.exe` | Malware delivery location                             |
| Additional tags               | **AsyncRAT, bitrat**                              | Indicates possible remote-access malware associations |
| URLhaus threat type           | **Malware download**                              | Confirms malicious delivery behavior                  |
| URL status                    | **Offline**                                       | Historical IOC; prior exposure remains relevant       |


$${\color{green}__Screenshot \space Analysis__}$$

The screenshot shows the URLhaus record for the exact executable URL identified in the phishing email. The entry labels it as a malware download and lists _CoinMiner_ among the associated tags, identifying the malware classification responsible for cryptocurrency mining.
<img width="1592" height="805" alt="image" src="https://github.com/user-attachments/assets/ef43deb1-e459-43be-a401-74805bc0a478" />

The same record also includes AsyncRAT and bitrat, suggesting that the delivery infrastructure may have supported more than one malicious payload or capability. The offline status indicates that the URL was no longer active during the lookup, not that the original email or any resulting infections were harmless.

Answer: **CoinMiner**

It worked! 🔥

#### ❔  5: Identifying the specific URLs malware requests is key to disrupting its communication channels and reducing its impact. Based on the previous analysis of the cryptocurrency malware sample, what does this malware request the URL?

$${\color{purple}__Objective__}$$
$${\color{red}__Analysis__}$$
$${\color{yellow}__Findings /\space IOC's__}$$
$${\color{green}__Screenshot \space Analysis__}$$

#### ❔  6: Understanding the registry entries added to the auto-run key by malware is crucial for identifying its persistence mechanisms. Based on the BitRAT malware sample analysis, what is the executable's name in the first value added to the registry auto-run key?

$${\color{purple}__Objective__}$$
$${\color{red}__Analysis__}$$
$${\color{yellow}__Findings /\space IOC's__}$$
$${\color{green}__Screenshot \space Analysis__}$$

#### ❔  7: Identifying the SHA-256 hash of files downloaded from a malicious URL is essential for tracking and analyzing malware activity. Based on the BitRAT analysis, what is the SHA-256 hash of the file previously downloaded and added to the autorun keys?

$${\color{purple}__Objective__}$$
$${\color{red}__Analysis__}$$
$${\color{yellow}__Findings /\space IOC's__}$$
$${\color{green}__Screenshot \space Analysis__}$$

#### ❔  8: Analyzing the HTTP requests made by malware helps in identifying its communication patterns. What is the URL in the HTTP request used by the loader to retrieve the BitRAT malware?

$${\color{purple}__Objective__}$$
$${\color{red}__Analysis__}$$
$${\color{yellow}__Findings /\space IOC's__}$$
$${\color{green}__Screenshot \space Analysis__}$$

#### ❔  9: Introducing a delay in malware execution can help evade detection mechanisms. What is the delay (in seconds) caused by the PowerShell command according to the BitRAT analysis?

$${\color{purple}__Objective__}$$
$${\color{red}__Analysis__}$$
$${\color{yellow}__Findings /\space IOC's__}$$
$${\color{green}__Screenshot \space Analysis__}$$

#### ❔  10: Tracking the command and control (C2) domains used by malware is essential for detecting and blocking malicious activities. What is the C2 domain used by the BitRAT malware?

$${\color{purple}__Objective__}$$
$${\color{red}__Analysis__}$$
$${\color{yellow}__Findings /\space IOC's__}$$
$${\color{green}__Screenshot \space Analysis__}$$

#### ❔  11: Understanding how malware exfiltrates data is essential for detecting and preventing data breaches. According to the AsyncRAT analysis, what is the Telegram Bot ID used by this malware?

$${\color{purple}__Objective__}$$
$${\color{red}__Analysis__}$$
$${\color{yellow}__Findings /\space IOC's__}$$
$${\color{green}__Screenshot \space Analysis__}$$

