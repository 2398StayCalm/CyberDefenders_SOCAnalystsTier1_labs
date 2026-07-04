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

Identify the OLE stream responsible for storing the Base64-encoded data embedded within the malicious document. Locating encoded content is an important step during static analysis because threat actors frequently use Base64 encoding to conceal payloads, commands, URLs, or additional stages of malware from basic detection mechanisms.

$${\color{red}__Analysis__}$$

To determine where the encoded content was stored, the document was first analyzed using the olevba utility. The initial olevba output indicated the presence of suspicious Base64-encoded strings within the document, confirming that additional inspection of the VBA-related streams was required.

The extracted content was then reviewed to identify the specific stream containing the encoded data. Examination of the VBA form streams revealed a large block of text consisting primarily of characters commonly associated with Base64 encoding. The output header displayed the exact stream path where this data was located, allowing the encoded content to be associated with a specific OLE stream.

To correlate the stream path with its numerical stream identifier, the oledump.py utility was used to enumerate all streams contained within the document. By matching the stream path identified during the VBA analysis with the stream listing generated by oledump.py, the stream responsible for storing the encoded data was identified. The analysis showed that the Base64 content resided in the stream _Macros/roubhaol/i09/o_, which corresponds to stream _34_ in the OLE structure.

$${\color{purple}__Findings\space /\space IOCs__}$$

- Tools Used:
  - olevba
  - oledump.py
- Encoded Content Type: Base64
- OLE Stream Path: Macros/roubhaol/i09/o
- Stream Number: 34
- Observation: Large encoded data blob stored within a VBA form stream

Answer: _Stream 34 (Macros/roubhaol/i09/o)_

$${\color{green}__Screenshot \space Analysis__}$$

<img width="1405" height="142" alt="image" src="https://github.com/user-attachments/assets/519a031f-fbf0-42cc-84d4-79cb115af52c" />

The screenshot shows the execution of the olevba sample.bin command against the suspicious document. This tool was used to extract VBA-related content and identify indicators such as encoded strings and suspicious macro behavior.

<img width="1902" height="582" alt="image" src="https://github.com/user-attachments/assets/18e9a78e-8b87-4336-a959-0f38839d5a7f" />

The screenshot displays the Base64-encoded content extracted from the document. The presence of a large encoded string indicates an attempt to conceal additional data or functionality within the VBA project.

<img width="1887" height="642" alt="image" src="https://github.com/user-attachments/assets/42420d0f-4236-4a84-8386-b62040a2a93f" />

The screenshot shows the stream header associated with the encoded content. The output identifies the storage location as Macros/roubhaol/i09/o, allowing the analyst to pinpoint the exact stream containing the Base64 data.

<img width="887" height="282" alt="image" src="https://github.com/user-attachments/assets/060d728d-e56e-4723-9cb7-4233b8fcff3c" />

The screenshot displays the oledump.py stream enumeration output. By correlating the previously identified stream path with the OLE stream listing, the analyst confirms that _Macros/roubhaol/i09/o_ corresponds to _stream 34_, which contains the Base64-encoded string.

It worked! 🔥

#### ❔  5: This document contains a user-form. Provide the name.

$${\color{orange}__Objective__}$$

Identify the name of the VBA UserForm embedded within the malicious Office document. UserForms are commonly used in Microsoft Office malware to store data, conceal payloads, hold encoded strings, or provide a mechanism for interacting with VBA code during execution.

$${\color{red}__Analysis__}$$

As part of the static analysis process, the document was examined using the olevba utility to enumerate all VBA components contained within the OLE structure. In addition to standard VBA modules (.bas) and class modules (.cls), olevba also identifies UserForms (.frm), which represent graphical forms and associated controls within a VBA project.

Reviewing the output revealed multiple VBA components, including a class module containing the Document_Open() auto-execution event and a UserForm component. The UserForm was clearly identified by the .frm extension, which indicates a form object rather than executable VBA source code.

The analysis showed the presence of a UserForm named roubhaol.frm. This form is particularly relevant because previous investigation identified associated streams under the _Macros/roubhaol/_ path that contained encoded data. This suggests the UserForm was likely leveraged to store additional content within the document, a technique frequently observed in malicious Office documents to hide payloads from casual inspection.

$${\color{purple}__Findings\space /\space IOCs__}$$

- Tool Used: olevba
- UserForm Name: roubhaol
- VBA Component Type: UserForm (.frm)
- Associated Stream Path: Macros/VBA/roubhaol
- Related Observation: Additional streams linked to this UserForm contained encoded data identified during previous analysis.

Answer: _roubhaol_

$${\color{green}__Screenshot \space Analysis__}$$

<img width="1132" height="612" alt="image" src="https://github.com/user-attachments/assets/ae5d2399-c8bc-4c44-97be-1ce544a84cc9" />

The screenshot shows the output of the olevba sample.bin command listing the VBA components embedded within the document. Among the extracted modules, roubhaol.frm is identified as a UserForm, confirming the name of the form object contained within the VBA project.

It worked! 🔥

#### ❔  6: This document contains an obfuscated Base64 encoded string; what value is used to pad (or obfuscate) this string?

$${\color{orange}__Objective__}$$

Determine the value used to obfuscate a Base64-encoded string embedded within the VBA macro. Malware authors frequently modify encoded strings by inserting junk characters or padding values to prevent straightforward decoding and evade static detection mechanisms.

$${\color{red}__Analysis__}$$

After identifying the stream containing the encoded data, the VBA source code was further examined using olevba. The extracted macro revealed a large string assigned to a variable that appeared to contain Base64 data mixed with additional characters. This is a common obfuscation technique used to hinder automated analysis and prevent direct Base64 decoding.

Reviewing the string construction within the VBA function showed a recurring sequence inserted throughout the encoded content. Instead of storing a clean Base64 string, the malware author repeatedly injected a fixed pattern between legitimate Base64 characters. During execution, the macro would remove or replace this padding value before decoding the resulting string and continuing execution.

The highlighted portion of the VBA code clearly exposes the repeated obfuscation sequence embedded throughout the encoded payload. Identifying this value is important because it enables an analyst to reconstruct the original Base64 data and continue the decoding process during malware analysis.

$${\color{purple}__Findings\space /\space IOCs__}$$

- Tool Used: olevba
- Obfuscation Technique: Junk string insertion within Base64 data
- Encoded Content Type: Base64
- Padding / Obfuscation Value: *2342772g3&gs7712ffvs626fq
- Purpose: Conceal the original Base64 string and hinder direct decoding

Answer: _2342772g3&*gs7712ffvs626fq_

$${\color{green}__Screenshot \space Analysis__}$$

<img width="1905" height="686" alt="image" src="https://github.com/user-attachments/assets/f17a95e5-ad55-43c9-b696-8f6f3e231318" />

The screenshot shows the VBA source code extracted from the document using olevba. Within the function, a large encoded string is assigned to a variable, and the recurring sequence _2342772g3&*gs7712ffvs626fq_ can be observed throughout the data. This repeated pattern serves as the obfuscation value inserted into the Base64 string to disguise the underlying payload.

#### ❔  7: What is the program executed by the Base64 encoded string?

$${\color{orange}__Objective__}$$

Determine which program is executed after decoding the obfuscated Base64 string embedded within the malicious Office document. Identifying the initial process launched by a macro is essential for understanding the malware's execution chain and post-exploitation behavior.

$${\color{red}__Analysis__}$$

After locating the Base64-encoded data stored within the UserForm stream, the next step was to remove the previously identified obfuscation value. The encoded content contained repeated occurrences of the padding string 2342772g3&*gs7712ffvs626fq, which prevented direct decoding of the payload.

To reconstruct the original encoded data, the obfuscation string was removed using CyberChef's Find / Replace operation. Once the junk data had been stripped, the resulting content resembled a valid Base64-encoded command. The cleaned string was then prepared for decoding to reveal the underlying payload.

Inspection of the reconstructed string immediately revealed the command prefix _powershell -e_. The -e (EncodedCommand) parameter is commonly used by threat actors to execute Base64-encoded PowerShell commands directly from the command line. This technique is frequently observed in malicious Office documents because it allows attackers to conceal the true command while leveraging a legitimate Windows administrative utility.

The discovery of a PowerShell execution command aligns with the document's malicious characteristics identified earlier, including automatic execution via Document_Open, encoded content, obfuscation techniques, and associations with the Emotet malware family.

$${\color{purple}__Findings\space /\space IOCs__}$$

- Tool Used:
  - olevba
  - CyberChef
- Obfuscation String Removed:
  - 2342772g3&*gs7712ffvs626fq
- Execution Method:
  - Base64-encoded PowerShell command
- Program Executed:
  - PowerShell
- Observed Command Prefix:
  - powershell -e

Answer: _PowerShell_

$${\color{green}__Screenshot \space Analysis__}$$

<img width="1897" height="685" alt="image" src="https://github.com/user-attachments/assets/ae3329ed-62b7-417b-9ef5-c716ea9d0377" />

The screenshot shows the extracted Base64-encoded content residing within the VBA form stream. The encoded data contains repeated occurrences of the obfuscation string, preventing direct decoding and indicating an attempt to conceal the payload.

<img width="1547" height="832" alt="image" src="https://github.com/user-attachments/assets/b635cb44-2a40-4e24-923e-54b699886e09" />

The screenshot displays CyberChef being used to remove the obfuscation value through a Find/Replace operation. Once the junk string is stripped from the data, the output immediately reveals the command prefix powershell -e, confirming that the decoded payload is intended to launch _PowerShell_.

It worked! 🔥

#### ❔  8: What WMI class is used to create the process to launch the Trojan?

$${\color{orange}__Objective__}$$

Identify the Windows Management Instrumentation (WMI) class leveraged by the malicious document to create and launch a process on the victim system. Determining the specific WMI class used provides insight into the malware's execution technique and helps defenders understand how the payload is spawned after macro execution.

$${\color{red}__Analysis__}$$

After removing the obfuscation string from the embedded Base64 data, the resulting content was decoded using CyberChef's From Base64 operation. Decoding the payload revealed a PowerShell script containing additional obfuscated code and execution logic.

Although the decoded output still contained inserted characters and formatting intended to hinder readability, key strings remained identifiable through manual inspection. Among these strings was a reference to a WMI process creation method. WMI is frequently abused by malware because it provides a legitimate Windows interface for process execution, system management, and remote administration while blending in with normal operating system activity.

Reviewing the decoded script exposed the string Win32_Process, which is a well-known WMI class used to create new processes through methods such as Create(). Malware families such as Emotet commonly leverage this technique to launch secondary payloads or execute PowerShell commands while avoiding more heavily monitored execution paths.

The presence of _Win32_Process_ within the decoded payload indicates that the malware intended to use the WMI process creation mechanism to execute the next stage of the attack.

$${\color{purple}__Findings\space /\space IOCs__}$$

- Tool Used: CyberChef
- Decoding Method:
  - Remove obfuscation string
  - Base64 decode payload
- Execution Mechanism: WMI
- WMI Class Identified: Win32_Process
- Common Associated Method: Create()

Answer: _Win32_Process_

$${\color{green}__Screenshot \space Analysis__}$$

<img width="1536" height="842" alt="image" src="https://github.com/user-attachments/assets/a8686fca-4551-48d7-b845-c4c5a949faf6" />

The screenshot shows CyberChef being used to decode the previously extracted Base64 payload after removing the junk padding string. Within the decoded output, the string Win32_Process is visible, revealing the WMI class referenced by the malicious script.
The highlighted section of the decoded content identifies Win32_Process, a Windows Management Instrumentation class commonly used to create and launch processes. Its presence confirms that the malware relied on WMI-based process creation to execute the Trojan payload.

It worked! 🔥

#### ❔  9: Multiple domains were contacted to download a Trojan. Provide the first FQDN as per the provided hint.

$${\color{orange}__Objective__}$$

Identify the first FQDN contacted by the malicious PowerShell payload to download the next-stage Trojan. Network indicators such as domains and URLs are critical IOCs because they enable defenders to create detection rules, perform threat hunting, and block malicious infrastructure.

$${\color{red}__Analysis__}$$

After decoding the Base64-encoded PowerShell payload and removing the additional character obfuscation, the resulting script became significantly more readable. The decoded code revealed a variable containing multiple URLs separated by delimiters. This list appears to represent fallback infrastructure that the malware attempts to contact sequentially until a payload is successfully retrieved.

To further deobfuscate the script, a second Find/Replace operation was performed to remove the inserted dot (.) characters that had been added throughout the PowerShell code. Once cleaned, the variable containing the download locations became visible and exposed several URLs used by the malware.

The first URL in the list begins with:

https   :    //    haoqunkong . com     /     bn/     s9w4tgcjl_f6669ugu_w4bj/

Extracting the hostname from this URL identifies the first FQDN contacted by the malware as _haoqunkong . com_. The script then proceeds to reference additional domains that likely serve as backup download locations if the initial request fails.

$${\color{purple}__Findings\space /\space IOCs__}$$

- Tool Used: CyberChef
- Decoding Method:
  - Remove junk string 2342772g3&*gs7712ffvs626fq
  - Base64 decode
  - Remove inserted . characters
- First Download URL:
  - _https:    //haoqunkong     .     com/bn/s9w4tgcjl_f6669ugu_w4bj/_
- First FQDN:
  - haoqunkong.com

Answer: _haoqunkong.com_

$${\color{green}__Screenshot \space Analysis__}$$

<img width="1530" height="852" alt="image" src="https://github.com/user-attachments/assets/b5136162-82e8-49da-be01-1cef58799e3a" />

The screenshot shows the initial Base64-decoded PowerShell script after removal of the junk padding string. Although the script remains partially obfuscated through inserted periods, multiple URLs can already be identified within the code.

<img width="1542" height="707" alt="image" src="https://github.com/user-attachments/assets/3713560e-a8f3-4aa3-9742-ac17fe0e302c" />

The screenshot shows the result of removing the inserted dot characters using CyberChef. The deobfuscated PowerShell script clearly reveals a list of download URLs, with the first entry pointing to _https://    haoqunkong    .    com/..._, confirming _haoqunkong     .     com_ as the first FQDN contacted by the malware.


It worked! 🔥

All Done! 🥇
