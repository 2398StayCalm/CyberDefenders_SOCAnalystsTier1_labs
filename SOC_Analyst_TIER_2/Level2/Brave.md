# 💻 Brave  
📌Link to the lab -> [https://cyberdefenders.org/blueteam-ctf-challenges/brave/]

### 📓 Scenario:
A memory image was acquired from a suspected compromised Windows workstation. The system belonged to a user flagged for potentially malicious activities, including unauthorized access attempts and unusual browsing patterns. The security team observed network activity to external IPs associated with encrypted communication services.

Your task is to analyze the provided memory dump to uncover details about the processes involved, identify active connections at the time of the compromise, and trace the usage patterns of specific applications.

### ❓Questions❓:

#### ❔  1: What time was the RAM image acquired according to the suspect system?

$${\color{red}__Analysis__}$$

To determine when the memory image was acquired, the memory dump was first loaded into Volatility 3 and examined using the windows.info plugin. This plugin extracts fundamental operating system metadata directly from kernel memory structures, including the system time recorded within the captured memory image. Because RAM acquisitions preserve the state of memory at the exact moment of collection, the reported system time provides a reliable indication of when the acquisition occurred on the suspect workstation.

The following command was executed: _python3 vol.py -f 20210430-Win10Home-20H2-64bit-memdump.mem windows.info_

$${\color{yellow}__Evidence \space Interpretation__}$$

The acquisition timestamp serves as a critical reference point for the investigation. All process activity, network connections, browser artifacts, and user actions recovered from memory can be correlated against this time to establish an accurate timeline of events surrounding the suspected compromise.

$${\color{green}__Screenshot \space Analysis__}$$

<img width="1327" height="517" alt="image" src="https://github.com/user-attachments/assets/298434ce-008a-4e8c-8153-c2e04bfa0d42" />

The screenshot shows the execution of the Volatility 3 windows.info plugin against the provided memory image. The output contains operating system metadata extracted from memory, including kernel information, Windows version details, and the SystemTime value. The SystemTime field indicates _2021-04-30 17:52_, which corresponds to the time at which the memory image was captured.

It worked! 🔥

#### ❔  2: What is the SHA256 hash value of the RAM image?

$${\color{red}__Analysis__}$$

To verify the integrity of the provided memory image and establish a forensic baseline, a cryptographic hash was calculated using the SHA256 hashing algorithm. Hashing is a standard digital forensic practice that produces a unique fixed-length value for a file, allowing investigators to verify that evidence has not been modified during acquisition, transfer, storage, or analysis.

The hash value was generated using the native sha256sum utility available in Kali Linux. 
The following command was executed against the memory image: _sha256sum 20210430-Win10Home-20H2-64bit-memdump.mem_

The utility processed the entire memory dump and returned a SHA256 digest of: _9bd01b1e7b19a3b2113bfb65e860fffd7a1630bdf2b18613d206ebf2aa0ea172_
This value uniquely identifies the memory image and can be used throughout the investigation to validate the integrity of the evidence and demonstrate that the analyzed file remained unchanged.

$${\color{yellow}__Evidence \space Interpretation__}$$

The calculated SHA256 hash serves as a digital fingerprint of the memory image. Any modification to the file, even a single-bit change, would result in a completely different hash value. Maintaining and documenting this value is essential for evidence verification, chain-of-custody procedures, and reproducibility of forensic findings.

$${\color{green}__Screenshot \space Analysis__}$$

<img width="982" height="137" alt="image" src="https://github.com/user-attachments/assets/c5a6b569-cb78-4860-84f1-c83b807bae38" />

The screenshot shows the execution of the sha256sum utility against the memory dump file from a Kali Linux terminal. The resulting output displays the calculated SHA256 hash alongside the filename, confirming successful integrity verification of the forensic evidence. The generated hash value is _9bd01b1e7b19a3b2113bfb65e860fffd7a1630bdf2b18613d206ebf2aa0ea172_.

It worked! 🔥

#### ❔  3: What is the process ID of brave.exe?

$${\color{red}__Analysis__}$$

To identify the Process ID (PID) associated with the Brave browser, the memory image was analyzed using Volatility 3's windows.pslist plugin. This plugin enumerates active and recently terminated processes by traversing the EPROCESS structures maintained by the Windows kernel. Process listings recovered from memory provide valuable information such as process names, identifiers, parent-child relationships, thread counts, creation times, and termination timestamps.

To simplify the output and focus on the target application, the process list was filtered using grep to locate entries containing the string brave.exe. The following command was executed: _python3 vol.py -f 20210430-Win10Home-20H2-64bit-memdump.mem windows.pslist | grep -2 -i "brave.exe"_

The filtered results revealed a process named brave.exe with a Process ID (PID) of _4856_. Additional information from the process listing showed that the browser was launched on 2021-04-30 17:48 UTC and terminated on 2021-04-30 17:50 UTC, indicating that Brave had been executed shortly before the memory acquisition occurred.

$${\color{yellow}__Evidence \space Interpretation__}$$

Identifying the PID of brave.exe is an important step in memory forensics because it enables correlation with other artifacts, including network connections, loaded modules, command-line arguments, handles, and browser-related memory structures. The PID serves as a unique identifier that can be used throughout the investigation to track the application's activity within the captured memory image.

$${\color{green}__Screenshot \space Analysis__}$$

<img width="1222" height="202" alt="image" src="https://github.com/user-attachments/assets/a28ad2ba-5818-4742-87c2-bc1bfc15f359" />

The screenshot shows the execution of the Volatility 3 windows.pslist plugin with output filtered to display entries related to brave.exe. The process listing contains key forensic attributes, including the process name, PID, parent process ID, creation time, and exit time. The output confirms that brave.exe was assigned PID 4856 at the time it was present on the system.

It worked! 🔥

#### ❔  4: How many established network connections were there at the time of acquisition?

$${\color{red}__Analysis__}$$

To determine the number of active network connections present when the memory image was acquired, the windows.netscan plugin from Volatility 3 was used. This plugin scans memory for Windows networking structures and reconstructs TCP and UDP socket information, including local and remote addresses, port numbers, connection states, associated process identifiers, and process ownership. Unlike live system utilities, memory-based network analysis can recover connection artifacts even when processes have already terminated.
The following command was initially executed to enumerate all network-related artifacts recovered from memory:
_python3 vol.py -f 20210430-Win10Home-20H2-64bit-memdump.mem windows.netscan_

The output contained multiple connection states, including LISTENING, CLOSE_WAIT, SYN_SENT, and ESTABLISHED. Since the objective was to identify active established communications at the time of acquisition, the results were filtered to display only connections in the ESTABLISHED state. The filtered output was then piped to wc -l to count the matching entries:
_python3 vol.py -f 20210430-Win10Home-20H2-64bit-memdump.mem windows.netscan | grep ESTABLISHED | wc -l_

The command returned a value of 10, indicating that a single network connection was in the ESTABLISHED state when the memory image was captured.

$${\color{yellow}__Evidence \space Interpretation__}$$

Established TCP connections represent active communication sessions between the host and a remote system. These connections are often among the most valuable network artifacts during memory analysis because they can reveal external systems with which the host was actively exchanging data. The presence of a single established connection provides a focused starting point for identifying the process responsible for the communication and determining whether it is related to the suspected activity.

$${\color{green}__Screenshot \space Analysis__}$$

<img width="1390" height="541" alt="image" src="https://github.com/user-attachments/assets/6d30f8d4-221f-42b9-8f8a-e7de5088028c" />

The first screenshot shows the execution of the Volatility 3 windows.netscan plugin against the memory image. The output lists recovered network artifacts, including local and remote addresses, ports, connection states, process identifiers, and owning processes. Multiple connection states are visible, demonstrating that the plugin successfully identified both active and historical network activity stored in memory.

<img width="991" height="74" alt="image" src="https://github.com/user-attachments/assets/5023d24c-db83-4bfa-a2e1-88fca22b18a7" />

The second screenshot shows the network scan results filtered to include only entries in the ESTABLISHED state. The output is further processed using wc -l, which returns a count of 10, confirming that there was a single established network connection present at the time of memory acquisition.

It worked! 🔥

#### ❔  5: Which domain name does Chrome have an established network connection with?

$${\color{red}__Analysis__}$$

identify the domain associated with Chrome's active network communication, memory-resident network artifacts were examined using the Volatility 3 windows.netscan plugin. Since the previous analysis identified a single ESTABLISHED connection, the network scan results were further filtered to locate connections owned by the Chrome browser process.

The following command was executed: _python3 vol.py -f 20210430-Win10Home-20H2-64bit-memdump.mem windows.netscan | grep ESTABLISHED | grep chrome_

The output revealed an active TCP connection associated with chrome.exe (PID 1840). The connection originated from the local host address 10.0.2.15 and communicated with the remote IP address 185.70.41.130 over TCP port 443, indicating encrypted HTTPS traffic.

To determine the corresponding domain name, the IP address was subjected to open-source intelligence (OSINT) analysis using AbuseIPDB. The lookup identified the host as belonging to Proton AG, with the hostname 185-70-41-130.protonmail.ch and the associated domain name _protonmail.ch_. This indicates that Chrome was maintaining an encrypted connection to Proton infrastructure at the time the memory image was acquired.

$${\color{yellow}__Evidence \space Interpretation__}$$

Correlating network artifacts recovered from memory with external intelligence sources enables attribution of network activity to specific services. The identified connection demonstrates that Chrome was actively communicating with Proton-operated infrastructure over HTTPS. Given the scenario's reference to encrypted communication services, this connection may be relevant to understanding the user's browsing behavior and communication patterns immediately prior to acquisition.

$${\color{green}__Screenshot \space Analysis__}$$

<img width="1147" height="122" alt="image" src="https://github.com/user-attachments/assets/1749102b-098b-410d-b75c-88c12e8d1bca" />

The first screenshot shows the windows.netscan output filtered for both ESTABLISHED connections and the Chrome browser process. The results identify a single active TCP connection owned by chrome.exe, communicating with the remote address 185.70.41.130 on port 443, confirming active encrypted network traffic.

<img width="800" height="660" alt="image" src="https://github.com/user-attachments/assets/b2cf4843-46c0-4fca-8fe1-6585b4fb575a" />

The second screenshot shows the OSINT lookup results for the remote IP address. The lookup associates the address with Proton AG and identifies the related domain as _protonmail.ch_, providing attribution for the external endpoint observed in the memory analysis.


#### ❔  6: What is the MD5 hash value of the process executable for PID 6988?

$${\color{red}__Analysis__}$$

To determine the MD5 hash value of the executable associated with PID 6988, it was first necessary to extract the process image from the memory dump. Volatility 3 provides the capability to recover executable files directly from process memory by reconstructing the in-memory image and writing it to disk for further analysis.

The process was identified as OneDrive.exe with PID 6988. The executable was extracted using the windows.pslist.PsList plugin with the --dump option, specifying the target process identifier and an output directory for the recovered file:

_python3 vol.py -f 20210430-Win10Home-20H2-64bit-memdump.mem -o dumpik/ windows.pslist.PsList --pid 6988 --dump_

The plugin successfully recovered the executable and saved it as: _6988.OneDrive.exe.0x1c0000.dmp_

After extraction, the integrity hash of the recovered file was calculated using the md5sum utility:
_md5sum 6988.OneDrive.exe.0x1c0000.dmp_

The command returned the MD5 hash value: _0b493d8e26f03ccd2060e0be85f430af_

This value represents the unique MD5 fingerprint of the executable recovered from memory and can be used for artifact identification, malware reputation checks, and comparison against known file hashes.

$${\color{yellow}__Evidence \space Interpretation__}$$

Hashing extracted process executables is a common forensic technique used to verify file integrity and identify known software or malicious binaries. The recovered MD5 hash can be compared against threat intelligence repositories, malware databases, or known-good software inventories to determine whether the executable corresponds to a legitimate application or a potentially modified binary.

$${\color{green}__Screenshot \space Analysis__}$$

<img width="1250" height="192" alt="image" src="https://github.com/user-attachments/assets/12c05329-2bf5-4845-b73b-524e12f73fed" />

The first screenshot shows the extraction of the executable associated with PID 6988 using Volatility 3. The output confirms that the process corresponds to OneDrive.exe and that the executable image was successfully dumped from memory into a file named _6988.OneDrive.exe.0x1c0000.dmp_.

<img width="591" height="206" alt="image" src="https://github.com/user-attachments/assets/9ae34d30-8e4f-4b7e-b6b2-66aa1b39331a" />

The second screenshot shows verification of the extracted file within the output directory followed by execution of the md5sum utility. The resulting MD5 hash value _0b493d8e26f03ccd2060e0be85f430af_ confirms successful extraction and provides a unique identifier for the recovered executable.

It worked! 🔥

#### ❔  7: Can you identify the word that begins at offset 0x45BE876 and is 6 bytes long?

$${\color{red}__Analysis__}$$

To identify the six-byte word beginning at memory offset 0x45BE876, a direct examination of the memory image was performed using the xxd utility. This tool enables low-level inspection of binary files by displaying hexadecimal values alongside their ASCII representation, making it useful for locating strings and validating data at specific offsets within a memory dump.

The analysis focused on the exact offset provided in the question. The following command was executed:
_xxd -s 0x45BE876 20210430-Win10Home-20H2-64bit-memdump.mem | grep -10 045be876_

The -s parameter instructs xxd to begin reading from the specified offset, while the output displays both hexadecimal and ASCII interpretations of the underlying memory contents. Examination of the ASCII column revealed the sequence: _hacker_
The string begins precisely at offset 0x45BE876 and consists of six bytes, matching the requirements of the task.

$${\color{yellow}__Evidence \space Interpretation__}$$

Direct string examination within a memory image can reveal user activity, application content, browser artifacts, search queries, and remnants of data processed by running applications. Identifying specific strings at known offsets helps validate memory artifacts and may provide contextual information regarding user actions or application usage at the time of acquisition.

$${\color{green}__Screenshot \space Analysis__}$$

<img width="881" height="245" alt="image" src="https://github.com/user-attachments/assets/b436f914-1d67-4d7d-843a-1bf611503a68" />

The screenshot shows the output of the xxd utility beginning at offset 0x45BE876 within the memory image. The hexadecimal values are displayed alongside their ASCII representation, allowing direct inspection of the memory contents. The ASCII column clearly shows the six-character word "hacker" starting at the specified offset, confirming the requested value.

It worked! 🔥

#### ❔  8: What is the creation date and time of the parent process of powershell.exe?

$${\color{red}__Analysis__}$$
$${\color{yellow}__Evidence \space Interpretation__}$$
$${\color{green}__Screenshot \space Analysis__}$$

#### ❔  9: What is the full path and name of the last file opened in notepad?

$${\color{red}__Analysis__}$$
$${\color{yellow}__Evidence \space Interpretation__}$$
$${\color{green}__Screenshot \space Analysis__}$$

#### ❔  10: How long did the suspect use Brave browser? (In Hours)

$${\color{red}__Analysis__}$$
$${\color{yellow}__Evidence \space Interpretation__}$$
$${\color{green}__Screenshot \space Analysis__}$$


