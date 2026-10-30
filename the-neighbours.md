# The Neighbours

**Outcome: you can place ClamAV among the other tools that claim overlapping jobs, and say what each is actually for.**

*This chapter is characterisation, not benchmark: the neighbours were not run in this guide's lab, and nothing here claims they were.*

## Windows Defender (Microsoft Defender Antivirus)

The built-in antivirus on Windows — and the first honest comparison. Defender adds what ClamAV deliberately lacks: constant **on-access** watching of every file as it is opened, behaviour monitoring, and deep hooks into the operating system, all maintained and updated by the platform's maker. On a Windows machine, Defender is the right primary defence and ClamAV is, at most, a second opinion for specific folders. ClamAV returns the compliment on Linux servers and mail gateways, where Defender is not the native citizen.

## The commercial suites

Paid antivirus products (for home and business) bundle signature scanning with behaviour analysis, firewalls, web filtering, support lines and compliance paperwork. You are paying for breadth, integration and someone to call. For a business with obligations, that can be money correctly spent. For a home server scanning its own mail and downloads, ClamAV covers the signature-scanning core of the job with nothing to renew — and with the unusual virtue that you can read exactly what it did.

## VirusTotal

Not a scanner but a **jury**: a service (run by Google) to which you submit a file or its hash, and sixty-odd engines — ClamAV among them — report their verdicts. Superb for a second opinion on one suspicious file. Two cautions keep it a neighbour and not a replacement: files you submit are shared with the service's subscribers (never submit anything private), and it scans nothing until you send it — it is a reference desk, not a guard.

## YARA

A different instrument that looks similar from a distance. YARA is a pattern-matching tool whose rules are written by *analysts* to hunt families of malware and describe their traits; security teams use it to sweep fleets and disk images while investigating. ClamAV's signatures are cousins of YARA rules — chapter 9's `.ndb` line is the family resemblance — but the audiences differ: YARA is a workbench for hunters; ClamAV is a guard for gates.

## Rootkit hunters (rkhunter, chkrootkit)

These answer a different question — not "is this file known-bad?" but "does this *already-running system* show signs of compromise?" They inspect system binaries, configuration and known hiding places. They complement rather than compete with ClamAV: one checks files at the gate, the other checks whether the house is already occupied.

## The short version

ClamAV's niche is exact: a free, open, scriptable **signature engine** for files you control — mail, uploads, downloads, servers. Inside that niche it is the standard tool. Outside it — guarding a Windows desktop in real time, judging one suspicious file by committee, hunting an intruder — it has neighbours whose whole job those things are, and this guide points at them rather than pretending.
