# 💻 MalDoc101 
📌Link to the lab -> [https://cyberdefenders.org/blueteam-ctf-challenges/maldoc101/]

### 📓 Scenario:
Threat actors often use living-off-the-land (LOTL) techniques, such as executing PowerShell, to advance their attacks and move away from macro code. This challenge aims to demonstrate how you can quickly analyze artifacts to extract important indicators of compromise (IOCs). The focus of this exercise is on static analysis techniques. As a security blue team analyst, analyze the provided artifacts and answer the questions.

### ❓Questions❓:

#### ❔  1: Multiple streams contain macros in this document. Provide the number of the highest one.

$${\color{orange}__Technical\space Analysis__}$$

As part of the static analysis process, the suspicious document was examined using the REMnux malware analysis distribution. REMnux provides a collection of forensic and reverse-engineering tools commonly used to analyze potentially malicious Microsoft Office documents without executing their contents. Since the supplied artifact was a binary OLE document, the analysis focused on identifying embedded VBA macro streams that could contain malicious code.

The oledump.py utility was used to enumerate all streams within the OLE file structure. This tool is particularly useful for detecting VBA projects and identifying streams that contain executable macro code. During the review of the output, special attention was given to the stream indicators displayed by oledump.py. Streams marked with a lowercase M indicate VBA macro code compressed with VBA compression, while a lowercase m identifies a VBA stream that contains executable macro code. These markers allow an analyst to quickly locate potentially malicious content without manually inspecting every stream.

The output revealed several macro-related streams within the Macros/VBA/ directory. By reviewing the numbered entries and focusing specifically on streams marked with the macro indicator, the highest numbered stream containing macro code was identified as _stream 16_ (Macros/VBA/roubhaol). This stream represents the highest VBA macro stream present in the document and serves as a potential location for further code extraction and analysis.

$${\color{green}__Screenshot \space Analysis__}$$

<img width="1857" height="857" alt="image" src="https://github.com/user-attachments/assets/44bd8ed6-a416-41b6-a3db-bfdc27a2a80d" />

The first screenshot shows the official REMnux documentation used to obtain and deploy the malware analysis environment. REMnux was selected because it includes numerous forensic and malware-analysis tools, including oledump.py, which is designed for analyzing OLE-based Microsoft Office documents.

<img width="1312" height="660" alt="image" src="https://github.com/user-attachments/assets/d37b6463-cd7c-4429-be1c-80416b363eaa" />

The second screenshot displays the output of the oledump.py sample.bin command, which enumerates all streams within the OLE document structure. Streams marked with macro indicators are visible under the Macros/VBA/ path, and the highest numbered stream containing VBA macro code is stream 16, making it the correct answer to the question.

It worked! 🔥

#### ❔  2: What event is used to begin the execution of the macros?

$${\color{orange}__Objective__}$$

Determine which VBA event is responsible for initiating macro execution when the Microsoft Office document is opened. Identifying auto-execution triggers is a critical step in malware analysis because threat actors frequently use these events to automatically launch malicious code without requiring additional user interaction.

$${\color{red}__Analysis__}$$

To identify the execution trigger, the document was analyzed using the olevba utility from the Oletools framework. This tool extracts VBA source code from Microsoft Office documents and performs automated detection of suspicious keywords, obfuscation techniques, and auto-execution functions commonly associated with malicious macros.

The analysis output revealed a VBA procedure named Document_Open(), which is a well-known Microsoft Word event that executes automatically whenever the document is opened. This event is commonly abused by attackers to initiate malicious actions such as launching PowerShell commands, spawning child processes, downloading payloads, or executing obfuscated VBA code immediately after a victim opens the document.

Further review of the olevba analysis confirmed the presence of an AutoExec indicator associated with Document_Open. The tool also identified several suspicious characteristics, including the use of object creation functions, string obfuscation techniques, Base64-encoded content, hexadecimal strings, and evidence of VBA stomping. These findings suggest that the document was intentionally crafted to conceal its true functionality and execute code automatically upon opening.

$${\color{purple}__Findings\space /\space IOCs__}$$

- Tool Used: olevba
- Auto-Execution Event: Document_Open
- Detection Type: AutoExec Macro
- Additional Suspicious Indicators:
  - Create
  - CreateObject
  - showwindow
  - chr
  - Base64-encoded strings
  - Hex-encoded strings
  - VBA Stomping detected

Answer: _Document_Open_

$${\color{green}__Screenshot \space Analysis__}$$

<img width="1827" height="481" alt="image" src="https://github.com/user-attachments/assets/4dd273a9-aff6-4645-9db1-079fc5288af5" />

The screenshot displays the output of the olevba sample.bin command and the extracted VBA source code. Within the macro stream, the Private Sub _Document_Open()_ procedure is visible, indicating that macro execution is configured to start automatically when the document is opened.

<img width="1687" height="747" alt="image" src="https://github.com/user-attachments/assets/0e24e3bb-b168-49c2-b74e-0d34b5cd00be" />

The screenshot shows the automated analysis results generated by olevba. The tool explicitly classifies Document_Open as an AutoExec function and highlights multiple suspicious indicators, including object creation methods, obfuscation techniques, encoded strings, and VBA stomping, all of which are commonly associated with malicious Office documents.

It worked! 🔥

#### ❔  3: What malware family was this maldoc attempting to drop?

$${\color{orange}__Objective__}$$

Identify the malware family associated with the malicious Office document by extracting a file hash and correlating it with threat intelligence sources. This step helps attribute the sample to a known malware family and provides additional context regarding its capabilities, distribution methods, and indicators of compromise.

$${\color{red}__Analysis__}$$

Following the initial macro analysis, the document was subjected to further static analysis to obtain a cryptographic hash that could be used for threat intelligence enrichment. The REMnux environment provides several utilities for this purpose, including Detect It Easy (DIE), which can quickly generate file metadata and hash values.

Using Detect It Easy, the hash of the suspicious document was extracted and recorded. This hash was then searched on VirusTotal, a multi-engine malware analysis platform that aggregates detections from numerous antivirus vendors and threat intelligence sources. Hash-based lookups are a common blue-team technique because they allow analysts to identify previously analyzed samples without needing to execute potentially malicious code.

The VirusTotal results showed that the sample was detected as malicious by a significant number of security vendors. In addition to the detection statistics, VirusTotal provided behavioral tags and malware family classifications associated with the file. The analysis identified the document as being linked to the Emotet malware family, a well-known malware operation historically used as a downloader and delivery mechanism for additional payloads such as banking trojans, ransomware, and other post-compromise tools.

$${\color{purple}__Findings\space /\space IOCs__}$$

- Tool Used: Detect It Easy (DIE)
- Threat Intelligence Source: VirusTotal
- File Type: Malicious Office Document
- Detection Ratio: 46/60 security vendors
- Malware Family: Emotet
- Family Labels:
  - Emotet
  - W97M
  - Emodldr
- Threat Categories:
  - Trojan
  - Downloader

Answer: _Emotet_

$${\color{green}__Screenshot \space Analysis__}$$

<img width="1912" height="916" alt="image" src="https://github.com/user-attachments/assets/10414fc0-37d7-445c-b43b-7ff38d2afd04" />

The screenshot shows Detect It Easy (DIE) running within the REMnux environment. The tool was used to calculate the file hash of the suspicious document, which serves as a unique identifier for threat intelligence lookups and malware correlation.

<img width="1885" height="877" alt="image" src="https://github.com/user-attachments/assets/b50090e0-220f-4886-b634-f8474618eaa5" />

The screenshot displays the VirusTotal analysis results for the submitted file hash. Multiple security vendors classify the sample as malicious, and the threat intelligence section identifies the malware family label as _Emotet_, confirming the malware family associated with the document.

It worked! 🔥

#### ❔  4: What stream is responsible for the storage of the base64-encoded string?

$${\color{orange}__Objective__}$$
$${\color{red}__Analysis__}$$
$${\color{purple}__Findings\space /\space IOCs__}$$
$${\color{green}__Screenshot \space Analysis__}$$

#### ❔  5: This document contains a user-form. Provide the name.

$${\color{orange}__Objective__}$$
$${\color{red}__Analysis__}$$
$${\color{purple}__Findings\space /\space IOCs__}$$
$${\color{green}__Screenshot \space Analysis__}$$

#### ❔  6: This document contains an obfuscated Base64 encoded string; what value is used to pad (or obfuscate) this string?

$${\color{orange}__Objective__}$$
$${\color{red}__Analysis__}$$
$${\color{purple}__Findings\space /\space IOCs__}$$
$${\color{green}__Screenshot \space Analysis__}$$

#### ❔  7: What is the program executed by the Base64 encoded string?

$${\color{orange}__Objective__}$$
$${\color{red}__Analysis__}$$
$${\color{purple}__Findings\space /\space IOCs__}$$
$${\color{green}__Screenshot \space Analysis__}$$

#### ❔  8: What WMI class is used to create the process to launch the Trojan?

$${\color{orange}__Objective__}$$
$${\color{red}__Analysis__}$$
$${\color{purple}__Findings\space /\space IOCs__}$$
$${\color{green}__Screenshot \space Analysis__}$$

#### ❔  9: Multiple domains were contacted to download a Trojan. Provide the first FQDN as per the provided hint.

$${\color{orange}__Objective__}$$
$${\color{red}__Analysis__}$$
$${\color{purple}__Findings\space /\space IOCs__}$$
$${\color{green}__Screenshot \space Analysis__}$$
