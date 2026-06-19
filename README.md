# Digital Forensic Analysis Lab

A toolkit to capture, analyze, and interpret digital forensic artifacts from
memory dumps, disk images, and system logs — built for incident response and
forensic investigation practice.

This project is based on the Digital Forensic Analysis Lab
(https://github.com/aw-junaid/cybersec-projects/tree/main/Projects/Defensive/Digital%20forensic%20analysis%20lab)
by Abdul Wahab Junaid (https://github.com/aw-junaid), used under the MIT License.

## What I changed

While testing the memory analysis module on Windows, I hit a runtime error in
the string-extraction function:

[-] Error extracting strings: '<=' not supported between instances of 'int' and 'bytes'

**Root cause:** the original code iterated directly over an open mmap
object byte-by-byte. On this Python/Windows combination, that iteration
returned bytes objects instead of plain integers, so the printable-ASCII
range check (32 <= byte <= 126) failed.

**Fix:** read the full mmap buffer into a bytes object first
(data = mm.read()), then iterate over that. Iterating over a real bytes
object always yields plain integers regardless of platform, which resolves
the crash.

## How to run

pip install pyopenssl

python forensic_lab.py memory -i memory.dmp -o memory_analysis.json
python forensic_lab.py disk -i disk.img -o disk_analysis.json
python forensic_lab.py artifacts -i evidence/ -o artifacts.json
python forensic_lab.py full -m memory.dmp -d disk.img -a evidence/ -o full_report.json

## License

MIT — see LICENSE file. Original work copyright (c) 2025 Abdul Wahab Junaid.