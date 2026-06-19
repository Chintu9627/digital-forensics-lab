\# 🔍 Digital Forensic Analysis Lab



!\[License](https://img.shields.io/badge/license-MIT-blue.svg)

!\[Python](https://img.shields.io/badge/python-3.x-yellow.svg)

!\[Platform](https://img.shields.io/badge/platform-Windows%20%7C%20Linux-lightgrey.svg)

!\[Status](https://img.shields.io/badge/status-active-success.svg)



A toolkit to \*\*capture, analyze, and interpret digital forensic artifacts\*\* from memory dumps, disk images, and system logs — built for incident response and forensic investigation practice.



> Based on the \[Digital Forensic Analysis Lab](https://github.com/aw-junaid/cybersec-projects/tree/main/Projects/Defensive/Digital%20forensic%20analysis%20lab) by \[Abdul Wahab Junaid](https://github.com/aw-junaid), used under the MIT License. See \[What I Changed](#-what-i-changed) for the fix contributed in this fork.



\---



\## 📑 Table of Contents



\- \[What I Changed](#-what-i-changed)

\- \[How to Run](#-how-to-run)

\- \[Algorithm Explanation](#-algorithm-explanation)

&#x20; - \[Memory Analysis](#1-memory-analysis)

&#x20; - \[Disk Analysis](#2-disk-analysis)

&#x20; - \[Artifact Extraction](#3-artifact-extraction)

&#x20; - \[Forensic Techniques](#4-forensic-techniques)

\- \[License](#-license)



\---



\## 🛠 What I Changed



While testing the memory analysis module on Windows, this error came up:



```

\[-] Error extracting strings: '<=' not supported between instances of 'int' and 'bytes'

```



| | |

|---|---|

| \*\*Root cause\*\* | The original code iterated directly over an open `mmap` object byte-by-byte. On this Python/Windows combination, that iteration returned `bytes` objects instead of plain integers, so the printable-ASCII range check (`32 <= byte <= 126`) failed. |

| \*\*Fix\*\* | Read the full `mmap` buffer into a `bytes` object first (`data = mm.read()`), then iterate over that. Iterating over a real `bytes` object always yields plain integers regardless of platform, which resolves the crash. |



\---



\## ▶️ How to Run



\### Python Version



```bash

\# Install dependencies

pip3 install pyopenssl



\# Analyze memory dump

python3 forensic\_lab.py memory -i memory.dmp -o memory\_analysis.json



\# Analyze disk image

python3 forensic\_lab.py disk -i disk.img -o disk\_analysis.json



\# Extract artifacts

python3 forensic\_lab.py artifacts -i /evidence/ -o artifacts.json



\# Complete forensic analysis

python3 forensic\_lab.py full -m memory.dmp -d disk.img -a /evidence/ -o full\_report.json

```



\### C Version



> ⚠️ The bug fix above applies to the \*\*Python version only\*\* — the C version's string extraction was not part of this fix.



```bash

\# Compile with OpenSSL

gcc -o forensic\_lab forensic\_lab.c -lssl -lcrypto



\# Analyze memory

./forensic\_lab memory memory.dmp memory\_report.txt



\# Analyze disk

./forensic\_lab disk disk.img disk\_report.txt



\# Generate full report

./forensic\_lab report memory.dmp disk.img forensic\_report.txt

```



\---



\## 🧩 Algorithm Explanation



\### 1. Memory Analysis



Memory analysis focuses on examining RAM captures to identify malicious activities that may not be visible on disk.



<details>

<summary><strong>🔹 Process Extraction</strong></summary>



\- Identifies all running and terminated processes present in memory

\- Displays Process ID (PID), Parent Process ID (PPID), and process hierarchy

\- Helps detect suspicious or hidden processes often used by malware

\- \*\*Example:\*\* Detecting an unknown process running under `explorer.exe`

</details>



<details>

<summary><strong>🔹 Network Analysis</strong></summary>



\- Maps active and historical network connections to specific processes

\- Identifies source and destination IP addresses, ports, and protocols

\- Helps investigators determine whether a process communicated with external systems

\- \*\*Example:\*\* Finding a suspicious process connected to a remote server

</details>



<details>

<summary><strong>🔹 String Extraction</strong></summary>



\- Extracts readable ASCII and Unicode strings from memory dumps

\- Reveals usernames, passwords, URLs, file paths, and commands

\- Useful for uncovering hidden evidence left in RAM

\- \*\*Example:\*\* Recovering a command-and-control server URL from memory

</details>



<details>

<summary><strong>🔹 Malware Detection</strong></summary>



\- Scans memory for suspicious indicators and malicious patterns

\- Identifies injected code, suspicious DLLs, and known malware signatures

\- Detects Indicators of Compromise (IOCs) that may suggest system compromise

\- \*\*Example:\*\* Detecting a malicious process injected into a legitimate application

</details>



\### 2. Disk Analysis



Disk analysis examines storage media to uncover files, logs, and system artifacts.



<details>

<summary><strong>🔹 File System Analysis</strong></summary>



\- Analyzes file system structures such as NTFS, FAT32, and EXT4

\- Extracts file metadata including creation, modification, and access times

\- Identifies hidden and suspicious files

\- \*\*Example:\*\* Investigating when a malicious file was created on a system

</details>



<details>

<summary><strong>🔹 Timeline Creation</strong></summary>



\- Builds a chronological sequence of system events

\- Correlates file activity, user actions, and system logs

\- Helps reconstruct attack timelines

\- \*\*Example:\*\* Determining the exact time malware was executed

</details>



<details>

<summary><strong>🔹 Deleted File Recovery</strong></summary>



\- Attempts to recover files removed from the file system

\- Analyzes unallocated disk space for recoverable evidence

\- Supports forensic investigations involving data destruction

\- \*\*Example:\*\* Recovering deleted documents related to an incident

</details>



<details>

<summary><strong>🔹 Registry Analysis</strong></summary>



\- Extracts artifacts from the Windows Registry

\- Identifies startup programs, installed software, USB devices, and user activity

\- Helps determine system configuration and persistence mechanisms

\- \*\*Example:\*\* Finding malware configured to run automatically at startup

</details>



\### 3. Artifact Extraction



Artifact extraction gathers evidence left behind by users and applications.



<details>

<summary><strong>🔹 System Artifacts</strong></summary>



\- Collects hostname, operating system details, and user account information

\- Provides a baseline understanding of the investigated machine

\- \*\*Example:\*\* Identifying the computer owner and operating system version

</details>



<details>

<summary><strong>🔹 User Activity Artifacts</strong></summary>



\- Retrieves recently opened files and executed commands

\- Analyzes clipboard history and user interaction records

\- Helps reconstruct user behavior

\- \*\*Example:\*\* Discovering recently accessed sensitive documents

</details>



<details>

<summary><strong>🔹 Network Artifacts</strong></summary>



\- Extracts ARP cache entries, DNS records, and connection history

\- Identifies systems previously contacted by the device

\- \*\*Example:\*\* Finding communication with suspicious domains

</details>



<details>

<summary><strong>🔹 Application Artifacts</strong></summary>



\- Analyzes browser history, downloads, cookies, and email data

\- Extracts evidence from communication and productivity applications

\- \*\*Example:\*\* Investigating whether a user visited phishing websites

</details>



\### 4. Forensic Techniques



These techniques ensure evidence integrity and support accurate investigations.



| Technique | What It Does | Example |

|---|---|---|

| \*\*Hashing\*\* | Generates cryptographic hashes (MD5, SHA-1, SHA-256) for files; verifies file integrity and detects modifications | Comparing hashes before and after acquisition |

| \*\*Timeline Analysis\*\* | Correlates events from multiple evidence sources; identifies relationships between activities and incidents | Linking file creation, execution, and network activity |

| \*\*Pattern Matching\*\* | Searches evidence for known malicious indicators using regex and IOC databases | Matching extracted artifacts against known threat indicators |

| \*\*Metadata Analysis\*\* | Extracts hidden information — authorship, timestamps, GPS data, software used | Identifying the creator and modification history of a document |



\---



\## 📄 License



MIT — see \[LICENSE](./LICENSE). Original work copyright (c) 2025 Abdul Wahab Junaid.

