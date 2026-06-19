# **Digital Forensic Analysis Lab**



### What the Tool Is For:

A toolkit to capture, analyze, and interpret digital forensic artifacts from
memory dumps, disk images, and system logs — built for incident response and
forensic investigation practice.



This project is based on the Digital Forensic Analysis Lab
(https://github.com/aw-junaid/cybersec-projects/tree/main/Projects/Defensive/Digital%20forensic%20analysis%20lab)
by Abdul Wahab Junaid (https://github.com/aw-junaid), used under the MIT License.



## **What I changed**



While testing the memory analysis module on Windows, I hit a runtime error in
the string-extraction function:

\[-] Error extracting strings: '<=' not supported between instances of 'int' and 'bytes'

**Root cause:** the original code iterated directly over an open mmap
object byte-by-byte. On this Python/Windows combination, that iteration
returned bytes objects instead of plain integers, so the printable-ASCII
range check (32 <= byte <= 126) failed.

**Fix:** read the full mmap buffer into a bytes object first
(data = mm.read()), then iterate over that. Iterating over a real bytes
object always yields plain integers regardless of platform, which resolves
the crash.



### How to run the code



##### Python Version:





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



##### C Version:



\# Compile with OpenSSL

gcc -o forensic\_lab forensic\_lab.c -lssl -lcrypto



\# Analyze memory

./forensic\_lab memory memory.dmp memory\_report.txt



\# Analyze disk

./forensic\_lab disk disk.img disk\_report.txt



\# Generate full report

./forensic\_lab report memory.dmp disk.img forensic\_report.txt



### Algorithm Explanation



##### 1\. Memory Analysis



Memory analysis focuses on examining RAM captures to identify malicious activities that may not be visible on disk.



###### • Process Extraction

Identifies all running and terminated processes present in memory.

Displays Process ID (PID), Parent Process ID (PPID), and process hierarchy.

Helps detect suspicious or hidden processes often used by malware.



Example: Detecting an unknown process running under explorer.exe.



###### • Network Analysis

Maps active and historical network connections to specific processes.

Identifies source and destination IP addresses, ports, and protocols.

Helps investigators determine whether a process communicated with external systems.



Example: Finding a suspicious process connected to a remote server.



###### • String Extraction

Extracts readable ASCII and Unicode strings from memory dumps.

Reveals usernames, passwords, URLs, file paths, and commands.

Useful for uncovering hidden evidence left in RAM.



Example: Recovering a command-and-control server URL from memory.



###### • Malware Detection

Scans memory for suspicious indicators and malicious patterns.

Identifies injected code, suspicious DLLs, and known malware signatures.

Detects Indicators of Compromise (IOCs) that may suggest system compromise.



Example: Detecting a malicious process injected into a legitimate application.



##### 2\. Disk Analysis



Disk analysis examines storage media to uncover files, logs, and system artifacts.



###### • File System Analysis

Analyzes file system structures such as NTFS, FAT32, and EXT4.

Extracts file metadata including creation, modification, and access times.

Identifies hidden and suspicious files.



Example: Investigating when a malicious file was created on a system.



###### • Timeline Creation

Builds a chronological sequence of system events.

Correlates file activity, user actions, and system logs.

Helps reconstruct attack timelines.



Example: Determining the exact time malware was executed.



###### • Deleted File Recovery

Attempts to recover files removed from the file system.

Analyzes unallocated disk space for recoverable evidence.

Supports forensic investigations involving data destruction.



Example: Recovering deleted documents related to an incident.



###### • Registry Analysis

Extracts artifacts from the Windows Registry.

Identifies startup programs, installed software, USB devices, and user activity.

Helps determine system configuration and persistence mechanisms.



Example: Finding malware configured to run automatically at startup.



##### 3\. Artifact Extraction



Artifact extraction gathers evidence left behind by users and applications.



###### • System Artifacts

Collects hostname, operating system details, and user account information.

Provides a baseline understanding of the investigated machine.



Example: Identifying the computer owner and operating system version.



###### • User Activity Artifacts

Retrieves recently opened files and executed commands.

Analyzes clipboard history and user interaction records.

Helps reconstruct user behavior.



Example: Discovering recently accessed sensitive documents.



###### • Network Artifacts

Extracts ARP cache entries, DNS records, and connection history.

Identifies systems previously contacted by the device.



Example: Finding communication with suspicious domains.



###### • Application Artifacts

Analyzes browser history, downloads, cookies, and email data.

Extracts evidence from communication and productivity applications.



Example: Investigating whether a user visited phishing websites.



##### 4\. Forensic Techniques



These techniques ensure evidence integrity and support accurate investigations.



###### • Hashing

Generates cryptographic hashes (MD5, SHA-1, SHA-256) for files.

Verifies file integrity and detects modifications.

Ensures evidence remains unchanged during analysis.



Example: Comparing hashes before and after acquisition.



###### • Timeline Analysis

Correlates events from multiple evidence sources.

Identifies relationships between activities and incidents.

Assists in reconstructing attack chains.



Example: Linking file creation, execution, and network activity.



###### • Pattern Matching

Searches evidence for known malicious indicators.

Uses regular expressions and IOC databases.

Detects suspicious IP addresses, domains, URLs, and file hashes.



Example: Matching extracted artifacts against known threat indicators.



###### • Metadata Analysis

Extracts hidden information from files and documents.

Reveals authorship, timestamps, GPS data, and software used.

Provides additional context during investigations.



Example: Identifying the creator and modification history of a document.



### License

MIT — see LICENSE file. Original work copyright (c) 2025 Abdul Wahab Junaid.

