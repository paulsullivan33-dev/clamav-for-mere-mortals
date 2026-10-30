# ClamAV for Mere Mortals

Most antivirus software is a subscription
and a black box: it watches your machine,
phones home to a company, and tells you
"protected" — trust it or don't.
**ClamAV** is the opposite arrangement.
It is a free, open source **antivirus
engine** that you install on your own
machine. It compares files against a
**signature database** — a list of known
bad patterns, kept by the project and
downloaded by a tool you run yourself —
and it tells you plainly what it found,
in words, on your screen, with an exit
code a script can act on.

This guide explains how ClamAV decides a
file is infected, how to install it, how
to keep its signatures fresh, and how to
scan files, folders and mail with it.
Then it does the part most antivirus
guides skip: it **proves** the scanner
works, without using a single real virus.
The proof uses the industry's harmless
test file, and then goes further — the
lab wrote two signatures of its own, for
files it invented, and watched the engine
catch those files by name.

The lab behind this guide ran the real
thing: **ClamAV 1.5.4**, the Ubuntu
package set, with the official signature
database downloaded in full — 3,628,245
signatures on the day of the run. Against
it the lab scanned clean files, a test
file, and its own signed inventions;
scanned a folder and copied the find
into quarantine; ran the **daemon** and
scanned through its socket; and broke
things on purpose — a missing file, an
empty database, a malformed signature —
so the error messages in this guide are
quoted, not imagined.

What the lab could not do, it did not
fake. There was no real malware anywhere
in this guide, by design: never test an
antivirus by fetching the real thing.
Mail-server wiring, on-access scanning
and the Windows and Mac builds are
taught from the documentation and
labelled as such. `criteria.md` keeps
the full ledger of what was run and
what was only read.

One rule before anything else, because
this is a security tool: **scan your own
machines, and files you have the right
to scan.** An antivirus is a defensive
tool for your own systems. Nothing in
this guide needs anyone else's files,
anyone else's network, or a real virus.

## What this guide covers

- [**What ClamAV is**](what-clamav-is.md) —
  the mental model: an engine, a list of
  known bad patterns, and a verdict per
  file — and what "antivirus" can and
  cannot mean
- [**The moving parts**](the-moving-parts.md) —
  the engine, the signature database,
  `clamscan`, `clamd` and `clamdscan`,
  `freshclam`, and which part holds what
- [**Installing ClamAV**](installing-clamav.md) —
  the package install, the pieces it
  lays down, and the version check that
  proves it runs
- [**Keeping signatures fresh**](keeping-signatures-fresh.md) —
  `freshclam`, the three database files,
  and how to read its report so you know
  your list is current
- [**Your first scan**](your-first-scan.md) —
  one file in, a verdict out: clean, the
  EICAR test file caught by name, and a
  file that merely talks about viruses
- [**Scanning folders and quarantine**](scanning-folders.md) —
  recursive scans, the scan summary, and
  copying a find into quarantine instead
  of deleting it
- [**The daemon — clamd**](the-daemon-clamd.md) —
  why the daemon exists: the database
  loaded once, scans answered over a
  socket in moments, many files at a time
- [**Reading the results**](reading-the-results.md) —
  FOUND and OK, the summary block, the
  three exit codes, and the errors that
  look like verdicts but are not
- [**Your own signatures**](your-own-signatures.md) —
  writing a pattern signature and a hash
  signature, and watching the engine use
  them — the whole mechanism, laid bare
- [**ClamAV and mail**](clamav-and-mail.md) —
  where a scanner sits in a mail server,
  and why mail is where ClamAV earns its
  keep — the wiring, documented
- [**Recipes**](recipes.md) — a nightly
  scan, a quarantine habit, scanning
  downloads and removable drives, each
  built from verified parts
- [**The neighbours**](the-neighbours.md) —
  Windows Defender, the commercial
  suites, VirusTotal, YARA: what each
  does that ClamAV doesn't, and vice
  versa
- [**Limits**](limits.md) — signatures
  only know what has been seen, a clean
  verdict is not a promise, and the
  other edges, stated plainly
- [**Troubleshooting**](troubleshooting.md) —
  the empty database, the malformed
  signature, the daemon that is not
  running, and the traps this guide's
  lab fell into
- [**Glossary**](glossary.md) — the
  terms, collected in one place

## What it doesn't cover

Finding or handling real malware — this
guide never touches the real thing, and
you should not either; the test file and
invented signatures prove everything a
beginner needs to prove. Removing an
infection from a compromised machine is
incident response, a different and
serious subject: a scanner's verdict is
where that work starts, not the whole
of it. Running ClamAV as a commercial
gateway product, and tuning it for a
large mail fleet, are likewise beyond
a mere mortals guide.

## How to use this guide

Read chapter 1 for the model, and
chapter 2 before installing anything —
knowing which part holds the database
turns later confusion into a named
problem. Chapters 3 and 4 get you the
programs and a current database;
chapter 5 scans in the same sitting.
Chapter 8 is the one to read slowly:
knowing the difference between *found*,
*clean* and *something went wrong* is
the difference between a scanner you
can trust and one you merely have. The
lab's transcripts live in
`clamav-lab/transcripts/`; its two
hand-written signatures are
`clamav-lab/labtest.ndb` and
`clamav-lab/labtest.hdb`, and its lab
configuration files sit beside them.

---

## Part of the series

This guide is one of the [For Mere Mortals](https://paulsullivan33-dev.github.io/mere-mortals/) guides: plain-language guides to the tools programmers and makers use.

Related guides:

- [Postfix & Dovecot for Mere Mortals](https://paulsullivan33-dev.github.io/postfix-and-dovecot-for-mere-mortals/)
- [Fail2Ban for Mere Mortals](https://paulsullivan33-dev.github.io/fail2ban-for-mere-mortals/)
- [Cybersecurity for Mere Mortals](https://paulsullivan33-dev.github.io/cybersecurity-for-mere-mortals/)

## License

The writing in this guide is licensed under [CC BY 4.0](https://github.com/paulsullivan33-dev/clamav-for-mere-mortals/blob/main/LICENSE). Code examples and lab scripts are licensed under the [MIT License](https://github.com/paulsullivan33-dev/clamav-for-mere-mortals/blob/main/LICENSE-CODE).
