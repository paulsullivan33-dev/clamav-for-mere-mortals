# What ClamAV Is

**Outcome: you can explain, in plain words, how a scanner decides a file is infected — and why its "clean" is a narrower promise than it sounds.**

Run this check on any machine that already has ClamAV:

```console
$ clamscan --version
ClamAV 1.5.4
```

## The one-sentence model

ClamAV compares every file you give it against a very long list of **known bad patterns**, and reports the files that match.

That is the whole mechanism. There is no artificial intelligence quietly judging your files, and no company server deciding for you. There is an **engine** (a program that knows how to look inside files), a **signature database** (the list), and a **verdict** for each file: the name of the pattern it matched, or `OK`.

A **signature** is a recognisable piece of a known bad file — a stretch of bytes, or a fingerprint of the whole file — written down in a compact form. When the engine finds that pattern inside a file it is scanning, it reports the file as infected and names the signature. Chapter 9 shows a signature up close, because the lab wrote two of its own; there is nothing magic in them.

## Where it came from, and where it lives

ClamAV began in 2002 as an open source project and is now maintained by Cisco Talos, the security research group that also maintains the official signature database. It runs on Linux, Windows and macOS, and it is the scanner inside a great many mail servers, file servers and hosting setups — often invisibly, doing its work where files arrive.

Its natural habitats, in order of how often mere mortals meet it:

- **Mail servers** — scanning every attachment as it passes through. This is the job ClamAV was born for, and chapter 10 is about it.
- **File and download servers** — scanning uploads before they are shared onwards.
- **Your own machine, on demand** — scanning a folder, a download, a USB stick, when you choose. This is the use this guide teaches hands-on.

## What "antivirus" honestly means here

Two honest limits belong in chapter 1, not buried at the end:

1. **A signature list only knows what has been seen.** A brand-new piece of malware, written this morning, matches nothing in the list until someone analyses it and a signature is published. Chapter 4 (keeping the list fresh) is therefore not housekeeping — it is half the product.
2. **A clean verdict means "no known pattern matched", nothing more.** It does not mean the file is safe, kind, or what it claims to be. Chapter 13 collects the other limits; hold these two from the start and none of them will surprise you.

ClamAV also has **heuristic** checks — pattern rules for things that *look* like tricks malware uses, such as certain kinds of packed or obfuscated files — and it can flag **phishing** signatures for mail. These widen the net. They do not change the basic model: match, or no match.

## The test file, and the rule about real malware

How do you prove a scanner works without a real virus? The industry settled that decades ago with the **EICAR test file**: a short, harmless text string that every antivirus agrees to treat as if it were a virus. It cannot infect anything — it is 68 characters of text whose only job is to be caught. Chapter 5 creates it and watches ClamAV catch it by name: `Eicar-Test-Signature`.

The rule for this whole guide follows from that: **never fetch real malware to test a scanner.** You gain nothing the test file cannot show you, and you take on real risk. This guide's lab never touched a real malicious file, and its transcripts are the better for it — every detection in them is explained.

The next chapter takes the machine apart: which program holds the list, which one does the scanning, and which one you actually talk to.
