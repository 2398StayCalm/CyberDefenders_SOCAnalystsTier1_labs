# 💻 Volatility Traces
📌Link to the lab -> [https://cyberdefenders.org/blueteam-ctf-challenges/volatility-traces/]

### 📓 Scenario:
On May 2, 2024, a multinational corporation identified suspicious PowerShell processes on critical systems, indicating a potential malware infiltration. This activity poses a threat to sensitive data and operational integrity.

You have been provided with a memory dump (memory.dmp) from the affected system. Your task is to analyze the dump to trace the malware's actions, uncover its evasion techniques, and understand its persistence mechanisms.

### ❓Questions❓:

#### ❔  1: Identifying the parent process reveals the source and potential additional malicious activity. What is the name of the suspicious process that spawned two malicious PowerShell processes?

Using Volatility3 tool we need to get process list from a provided memory dump. To do that we execute a windows.pslist command for Volatility 3.

<img width="1628" height="173" alt="image" src="https://github.com/user-attachments/assets/7a8e1000-2d79-4f3a-9425-5cc6cf59f1f4" />

With that command we cannot establish a parent process of those two PowerShell processes. For that we use a different command - windows.pstree. Additionaly we filter for processes related to the powershell.exe.

<img width="1901" height="136" alt="image" src="https://github.com/user-attachments/assets/5a8bfa04-566d-4051-887a-11dee0d47a9d" />

It worked! 🔥

#### ❔  2: By determining which executable is utilized by the malware to ensure its persistence, we can strategize for the eradication phase. Which executable is responsible for the malware's persistence?

Here we look through the psscan report that was provided in Artifacts directory to continue investigation. As we know the PID of malicious PowerShell processes and their parent process PID, we search for antoher process that is related to it by being a parent process.

<img width="1518" height="168" alt="image" src="https://github.com/user-attachments/assets/3b7a5914-279b-4d12-9f89-50453822b7b4" />

It worked! 🔥

#### ❔  3: Understanding child processes reveals potential malicious behavior in incidents. Aside from the PowerShell processes, what other active suspicious process, originating from the same parent process, is identified?

This one comes straight from the last solution. It's gonna be the only one that left ;)

It worked! 🔥

#### ❔  4: Analyzing malicious process parameters uncovers intentions like defense evasion for hidden, stealthy malware. What PowerShell cmdlet used by the malware for defense evasion?

Once we got PowerShell process PID, we search for commands that were executed with it. There is one command that got our attention. This command is used to add exclusions for file name extensions, paths, and processes, and to add default actions for high, moderate, and low threats. All of this is about modifying settings for Windows Defender.

<img width="1845" height="116" alt="image" src="https://github.com/user-attachments/assets/7d2ce187-2b75-46f0-a83a-2f02ca8e266c" />

It worked! 🔥

#### ❔  5: Recognizing detection-evasive executables is crucial for monitoring their harmful and malicious system activities. Which two applications were excluded by the malware from the previously altered application's settings?

For this we simply grep the powershell command from windows.cmdline using Volatility3 again and we can see two applications that were excluded.

<img width="1736" height="58" alt="image" src="https://github.com/user-attachments/assets/3c0bddda-033e-4c86-9b88-ab4729560e8c" />

It worked! 🔥

#### ❔  6: What is the specific MITRE sub-technique ID associated with PowerShell commands that aim to disable or modify antivirus settings to evade detection during incident analysis?

OSINT is the best way to quickly establish the sub-technique so just use any browser and do the research. It should appear easily.

<img width="998" height="180" alt="image" src="https://github.com/user-attachments/assets/c238f919-2dda-4ae1-8454-767ae069909d" />

It worked! 🔥

#### ❔  7: Determining the user account offers valuable information about its privileges, whether it is domain-based or local, and its potential involvement in malicious activities. Which user account is linked to the malicious processes?

A user is easy to find if you again search the command lines that were used to exclude malicious applications. It appears in the application's path.

It worked! 🔥

We've done it! 🥇
