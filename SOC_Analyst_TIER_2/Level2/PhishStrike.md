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

Identify the external URL requests made by the cryptocurrency-mining malware sample to determine its communication and secondary payload infrastructure.

$${\color{red}__Analysis__}$$

URLhaus provided the SHA-256 hash for the payload classified as CoinMiner:
**453fb1c4b3b48361fa8a67dcedf1eaec39449cb5a146a7770c63d1dc0d7562f0**

The hash was searched in VirusTotal, where the sample was detected as malicious by 54 of 70 security vendors. Under the sample’s relationships, VirusTotal recorded two contacted URLs. The primary external request shown is:

_http:   //ripley.   studio/loader/uploads/Qanjtbrbv.jpeg_

Despite the .jpeg extension, the resource should not be assumed to be a legitimate image. Malware frequently uses misleading file extensions to disguise configuration data, additional payloads, or command-and-control content. The sample also contacted http:   //107.175.247.199   /loader/server.exe, indicating possible retrieval of another executable component.

$${\color{yellow}__Findings /\space IOC's__}$$

| Indicator         | Value                                                                    | Assessment                                            |
| ----------------- | ------------------------------------------------------------------------ | ----------------------------------------------------- |
| Requested URL     | `http:   //ripley.   studio/loader/uploads/Qanjtbrbv.jpeg`               | Suspicious external resource requested by the malware |
| Secondary URL     | `http:   //107.175.247.199   /loader/server.exe`                         | Possible additional executable payload                |
| Domain            | `ripley.studio`                                                          | Malware-associated contacted domain                   |
| Payload SHA-256   | `453fb1c4b3b48361fa8a67dcedf1eaec39449cb5a146a7770c63d1dc0d7562f0`       | CoinMiner sample identifier                           |
| VirusTotal result | **54/70 detections**                                                     | Strong malicious classification                       |


$${\color{green}__Screenshot \space Analysis__}$$

The first screenshot shows the URLhaus payload-delivery records for the malicious invoice URL. The CoinMiner entry provides the SHA-256 hash used to pivot into VirusTotal for deeper behavioral analysis.
<img width="1310" height="832" alt="image" src="https://github.com/user-attachments/assets/89d82025-6825-40f1-9477-d301306bddba" />

The second screenshot shows the VirusTotal relationships for the malware sample. It records requests to http://   ripley.   studio/loader/uploads/Qanjtbrbv.jpeg and http:   //107.175.247.199   /loader/server.exe, revealing infrastructure that should be blocked and searched for in proxy, DNS, and endpoint telemetry.
<img width="1042" height="687" alt="image" src="https://github.com/user-attachments/assets/ab329aa9-11b7-4cd7-8d9c-fe3b5c286691" />

Answer: **http://   ripley.   studio/loader/uploads/Qanjttrbv.jpeg**

It worked! 🔥

#### ❔  6: Understanding the registry entries added to the auto-run key by malware is crucial for identifying its persistence mechanisms. Based on the BitRAT malware sample analysis, what is the executable's name in the first value added to the registry auto-run key?

$${\color{purple}__Objective__}$$

Identify the executable name associated with the first registry value used by the BitRAT sample for persistence. Registry-based auto-run mechanisms allow malware to execute again when the affected user logs in or the system restarts.

$${\color{red}__Analysis__}$$

The BitRAT payload was identified in URLhaus using the SHA-256 hash:
bf7628695c2df7a3020034a065397592a1f8850e59f9a448b555bc1c8c639539

The hash was then investigated in VirusTotal’s behavioral results. Within the registry activity, the sample accessed an entry referencing the following executable path:
_C:\Users\user\AppData\Roaming\Ozndcoodb\Jzwix.exe_

The executable name is therefore Jzwix.exe. Its placement under the user’s AppData\Roaming directory is suspicious because malware commonly stores payloads in user-writable locations to avoid requiring administrator privileges and to support persistence.

$${\color{yellow}__Findings /\space IOC's__}$$

| Indicator       | Value                                                              | Assessment                            |
| --------------- | ------------------------------------------------------------------ | ------------------------------------- |
| Executable name | **Jzwix.exe**                                                      | BitRAT persistence-related executable |
| File path       | `C:\Users\user\AppData\Roaming\Ozndcoodb\Jzwix.exe`                | Suspicious user-profile location      |
| Malware family  | **BitRAT**                                                         | Remote-access Trojan                  |
| SHA-256         | `bf7628695c2df7a3020034a065397592a1f8850e59f9a448b555bc1c8c639539` | BitRAT sample identifier              |

$${\color{green}__Screenshot \space Analysis__}$$

The first screenshot shows the URLhaus payload-delivery table for the malicious URL. The highlighted entry associates the SHA-256 hash bf7628695c2df7a3020034a065397592a1f8850e59f9a448b555bc1c8c639539 with the BitRAT signature.
<img width="1292" height="815" alt="image" src="https://github.com/user-attachments/assets/41b2615e-226d-4c53-9df5-7eab2cfb8405" />

The second screenshot shows VirusTotal behavioral registry activity for that sample. The highlighted registry entry references _C:\Users\user\AppData\Roaming\Ozndcoodb\Jzwix.exe_, confirming that the executable name used in the persistence-related entry is _Jzwix.exe_.
<img width="1037" height="696" alt="image" src="https://github.com/user-attachments/assets/02ea6fdf-a315-4bc3-a01a-41a4389eda53" />

Answer: **Jzwvix.exe**

It worked! 🔥

#### ❔  7: Identifying the SHA-256 hash of files downloaded from a malicious URL is essential for tracking and analyzing malware activity. Based on the BitRAT analysis, what is the SHA-256 hash of the file previously downloaded and added to the autorun keys?

$${\color{purple}__Objective__}$$

Confirm the SHA-256 hash of the BitRAT executable that was downloaded from the malicious URL and later referenced in the registry-based autorun activity.

$${\color{red}__Analysis__}$$

The BitRAT payload listed in URLhaus has the SHA-256 hash:
_bf7628695c2df7a3020034a065397592a1f8850e59f9a448b555bc1c8c639539_

This hash was correlated with the previously identified executable Jzwix.exe, located at:
C:\Users\user\AppData\Roaming\Ozndcoodb\Jzwix.exe

VirusTotal confirms that the same file path is associated with the identical SHA-256 value. This establishes a direct relationship between the payload retrieved from the malicious URL and the executable used in the persistence-related registry activity.

$${\color{yellow}__Findings /\space IOC's__}$$

| Indicator      | Value                                                              | Assessment                         |
| -------------- | ------------------------------------------------------------------ | ---------------------------------- |
| SHA-256        | `bf7628695c2df7a3020034a065397592a1f8850e59f9a448b555bc1c8c639539` | BitRAT payload identifier          |
| Executable     | `Jzwix.exe`                                                        | File linked to autorun persistence |
| File path      | `C:\Users\user\AppData\Roaming\Ozndcoodb\Jzwix.exe`                | Suspicious user-profile location   |
| Malware family | **BitRAT**                                                         | Remote-access Trojan               |

$${\color{green}__Screenshot \space Analysis__}$$

The first screenshot shows the URLhaus payload-delivery table, where the highlighted SHA-256 hash is associated with the BitRAT signature. This provides the original payload identifier retrieved from the malicious download location.
<img width="1350" height="267" alt="image" src="https://github.com/user-attachments/assets/befcb29d-849e-45f4-b90a-6b78ef1ccbfd" />

The second screenshot shows the VirusTotal file details for Jzwix.exe. The displayed SHA-256 value matches the URLhaus record exactly, confirming that the executable added to the autorun-related registry activity is the same BitRAT payload.
<img width="840" height="82" alt="image" src="https://github.com/user-attachments/assets/f49c1476-4247-4bf2-8bd4-e31a97b6fb08" />

Answer: **bf7628695c2df7a3020034a065397592a1f8850e59f9a448b555bc1c8c639539**

It worked! 🔥

#### ❔  8: Analyzing the HTTP requests made by malware helps in identifying its communication patterns. What is the URL in the HTTP request used by the loader to retrieve the BitRAT malware?

$${\color{purple}__Objective__}$$

Identify the HTTP request used by the loader to retrieve the BitRAT payload and document the associated malware-delivery infrastructure.

$${\color{red}__Analysis__}$$

VirusTotal’s behavioral analysis shows that the loader issued an HTTP GET request to:
_http:   //107.175.247.199   /loader/server.exe_

The request returned an HTTP 200 response, indicating that the file was successfully available to the malware during analysis. The response was identified as application/x-msdos-program, which is consistent with a Windows executable rather than a legitimate invoice or document.

The URL is hosted on the same malicious IP address identified earlier in the phishing email. This confirms that the loader contacted the attacker-controlled server to retrieve an additional executable associated with BitRAT.

$${\color{yellow}__Findings /\space IOC's__}$$

| Indicator            | Value                                            | Assessment                        |
| -------------------- | ------------------------------------------------ | --------------------------------- |
| BitRAT retrieval URL | `http:   //107.175.247.199   /loader/server.exe` | Loader download location          |
| Host IP              | **107.175.247.199**                              | Malicious delivery infrastructure |
| HTTP method          | **GET**                                          | Payload retrieval request         |
| HTTP status          | **200**                                          | Resource successfully returned    |
| Content type         | `application/x-msdos-program`                    | Windows executable content        |


$${\color{green}__Screenshot \space Analysis__}$$

The screenshot shows the Network Communication section of VirusTotal’s behavioral report. The highlighted request confirms that the loader contacted http:   //107.175.247.199   /loader/server.exe and received a successful 200 response.
<img width="1541" height="321" alt="image" src="https://github.com/user-attachments/assets/a22f05ad-4359-4c29-91e3-fc91bc3a2176" />

The response headers identify the downloaded content as a Windows executable. This supports the conclusion that the URL was used to retrieve the BitRAT payload from the previously identified malicious server.

Answer: **http:   //107.175.247.199   /loader/server.exe**

It worked! 🔥

#### ❔  9: Introducing a delay in malware execution can help evade detection mechanisms. What is the delay (in seconds) caused by the PowerShell command according to the BitRAT analysis?

$${\color{purple}__Objective__}$$

Determine the execution delay introduced by the encoded PowerShell command used by the BitRAT sample. Delayed execution is commonly used to evade sandbox analysis and postpone malicious activity until automated monitoring has ended.

$${\color{red}__Analysis__}$$

VirusTotal’s behavioral analysis shows a PowerShell command executed with the -enc parameter, indicating that the command content was Base64-encoded. The encoded value was extracted and decoded in CyberChef.

The decoded output reads: _Start-Sleep -Seconds 50_

This command pauses PowerShell execution for 50 seconds before continuing. Such a delay can help malware evade automated analysis environments that monitor samples only for a limited period.

$${\color{yellow}__Findings /\space IOC's__}$$

| Indicator         | Value                     | Assessment                          |
| ----------------- | ------------------------- | ----------------------------------- |
| PowerShell action | `Start-Sleep -Seconds 50` | Introduces delayed execution        |
| Delay             | **50 seconds**            | Likely sandbox-evasion behavior     |
| Encoding          | **Base64**                | Obscures command content            |
| Interpreter       | `powershell.exe`          | Used to execute the encoded command |

$${\color{green}__Screenshot \space Analysis__}$$

The first screenshot shows the VirusTotal Shell Commands section, where powershell.exe is launched with the -enc argument and a Base64-encoded command. This indicates an attempt to conceal the command’s actual purpose from basic inspection.
<img width="1091" height="287" alt="image" src="https://github.com/user-attachments/assets/ed180d0b-157d-4dbe-b95c-a2c3900f194b" />

The second screenshot shows the value decoded in CyberChef. The output reveals Start-Sleep -Seconds 50, confirming that the malware delays execution for _50 seconds_.
<img width="1092" height="540" alt="image" src="https://github.com/user-attachments/assets/65b31e6d-3da8-4eb6-aa01-6c53db1af129" />

Answer: **50**

It worked! 🔥

#### ❔  10: Tracking the command and control (C2) domains used by malware is essential for detecting and blocking malicious activities. What is the C2 domain used by the BitRAT malware?

$${\color{purple}__Objective__}$$

Identify the command-and-control domain used by the BitRAT sample so it can be blocked and monitored across DNS, proxy, firewall, and endpoint telemetry.

$${\color{red}__Analysis__}$$

The BitRAT SHA-256 hash was searched in MalwareBazaar, where the sample was confirmed as malicious and linked to an ANY.RUN sandbox report. The sandbox network activity showed two contacted domains: ripley  .  studio and _gh9st  .  mywire  .  org_.

ripley.studio was already associated with payload delivery in the earlier investigation. The domain gh9st.mywire.org appears as the separate remote communication endpoint and is therefore identified as the BitRAT command-and-control domain.

The use of a dynamic DNS-style domain is consistent with malware infrastructure designed to remain flexible and difficult to track. Security teams should block the domain and review historical DNS and network logs for any connections from institutional systems.

$${\color{yellow}__Findings /\space IOC's__}$$

| Indicator      | Value                        | Assessment                                |
| -------------- | ---------------------------- | ----------------------------------------- |
| C2 domain      | **gh9st  .  mywire  .  org** | BitRAT command-and-control infrastructure |
| Resolved IP    | **162.191.38.27**            | IP observed in sandbox network activity   |
| Payload domain | `ripley.studio`              | Associated with earlier payload delivery  |
| Malware family | **BitRAT**                   | Remote-access Trojan                      |


$${\color{green}__Screenshot \space Analysis__}$$

The first screenshot shows the MalwareBazaar record for the BitRAT sample and confirms the SHA-256 hash used for the investigation. The sample is identified as BitRAT and has multiple vendor detections.
<img width="1372" height="771" alt="image" src="https://github.com/user-attachments/assets/83d8d164-b8ee-4e03-9bd8-b95d7ac54b14" />

The second screenshot shows the vendor intelligence section containing a link to an ANY.RUN analysis. This provides the pivot from static threat intelligence into dynamic sandbox behavior.
<img width="1312" height="432" alt="image" src="https://github.com/user-attachments/assets/93847472-0eae-4b49-a616-8f17dd51914e" />

The third screenshot shows the sandbox network activity, where _gh9st   .   mywire   .   org_ communicates with the sample and resolves to 162.191.38.27. Because ripley.studio was already tied to payload delivery, gh9st.mywire.org is the stronger C2 indicator.
<img width="1247" height="212" alt="image" src="https://github.com/user-attachments/assets/876d54bd-21a9-401a-80cd-df8842cc93b4" />

Answer: **gh9st  .  mywire  .  org**

It worked! 🔥

#### ❔  11: Understanding how malware exfiltrates data is essential for detecting and preventing data breaches. According to the AsyncRAT analysis, what is the Telegram Bot ID used by this malware?

$${\color{purple}__Objective__}$$

Identify the Telegram Bot ID used by the analyzed malware sample to communicate with Telegram’s API. This value can help defenders trace command-and-control or data-exfiltration activity associated with the malware.

$${\color{red}__Analysis__}$$

The sample was analyzed in the Recorded Future Triage sandbox, where its network activity showed an HTTPS request to the Telegram Bot API:
https://  api  .  telegram  .  org/bot5610920260:[REDACTED_TOKEN]/getUpdates?offset=-5

Telegram bot credentials use the format <bot_id>:<token>. Therefore, the numeric value before the colon is the Bot ID:
_bot5610920260_

The /getUpdates endpoint indicates that the malware was polling Telegram for messages or commands. The bot token is intentionally redacted because it is an active authentication credential and should not be reproduced in a report.

A labeling inconsistency is visible in the sandbox overview, which identifies the family as BitRAT even though the question refers to AsyncRAT. The network evidence still clearly exposes the Telegram Bot ID associated with this analyzed sample.

$${\color{yellow}__Findings /\space IOC's__}$$

| Indicator       | Value                         | Assessment                             |
| --------------- | ----------------------------- | -------------------------------------- |
| Telegram Bot ID | **5610920260**                | Bot account used by the malware        |
| API host        | `api  .  telegram  .  org`    | Telegram communication service         |
| API method      | `/getUpdates`                 | Polls the bot for messages or commands |
| Remote address  | `149.154.167.220:443`         | Telegram infrastructure observed       |
| Bot token       | **Redacted**                  | Sensitive authentication credential    |

$${\color{green}__Screenshot \space Analysis__}$$

The first screenshot shows the sandbox report for the analyzed sample, including its SHA-256 value and a maximum malicious score of 10/10. The report also indicates persistence and Trojan behavior, although the displayed family classification differs from the AsyncRAT label used in the question.
<img width="1896" height="665" alt="image" src="https://github.com/user-attachments/assets/ddaf95e0-19dd-4b04-9c20-ba07ee75c5e5" />

The second screenshot shows the malware’s HTTP request to api  .  telegram  .  org. The request path begins with _/bot5610920260_:, confirming that the Telegram Bot ID is 5610920260, while the remaining string is the sensitive bot token.
<img width="1571" height="271" alt="image" src="https://github.com/user-attachments/assets/4f3b42ab-7704-4887-8836-1b1f410505dc" />

Answer: **bot5610920260**

It worked! 🔥

All Done! 🥇

