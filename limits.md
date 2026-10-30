# Limits

**Outcome: you can recite what a ClamAV verdict does not promise, before you are tempted to rely on it for more.**

An antivirus is easy to over-trust precisely because its verdicts are so tidy. These are the edges, collected in one place as this series insists.

## The list is a history book

Signatures describe malware that has been *found, analysed and published*. New malware — written today, or an old one repacked so its bytes differ — matches nothing until the world's analysts catch up and a daily update carries the new signature. Freshness narrows the gap (chapter 4); nothing closes it. A scanner is a strong defence against the known, and a partial one against the new.

## Clean is not safe

`OK` means *no loaded signature matched*. It does not mean the file is harmless, honest, or what its name claims. A clean scan of a download from a dubious source is one fact, not an acquittal. Keep hold of chapter 5's essay in both directions: words don't convict a file, and a verdict doesn't bless it.

## Matches can be wrong

Signatures occasionally match innocent files — a shared code library, a similar byte pattern, a heuristic that reaches too far. The project's own database even has a category (PUA, *potentially unwanted applications*) for tools that are legitimate in one pair of hands and a nuisance in another, off by default in the lab's runs. This is why chapter 6 quarantines instead of deleting, and why an unexpected `FOUND` on a file you trust is the start of a check (a second opinion, the file's source, its hash against reference lists), not an instant execution.

## It scans files, not behaviour

ClamAV examines files *at rest*: as they sit, arrive, or are handed to it. It does not watch what a running program does, does not sandbox a suspect and observe it, and — outside mail filters and separate on-access plumbing it can be wired into — does not stand between a user and a double-click. A machine protected only by on-demand scans is protected at the moments someone remembers to scan.

## Removal is not its job

A `FOUND` verdict identifies; it does not disinfect. Deleting the file (chapter 6) removes that file. If a machine has actually been running malware, the honest next step is incident response — treat the system as compromised, preserve what you need, reinstall from known-good media — not a victory lap because the scanner now says clean.

## Scale and cost are real

The database is millions of signatures and the load is seconds-to-minutes of CPU and a sizeable block of memory, paid per `clamscan` run or once by the daemon. On a small machine, scanning *everything, always* is a budget decision, not a default. Choose the ground that matters — mail, uploads, downloads, shared folders — and scan it well.

## And the boundary of this guide

The lab verified the engine, the updates, the daemon and hand-written signatures on one Linux machine with test files. It did not measure detection rates against real malware (by design and by rule — chapter 1), did not run the Windows or macOS builds, and did not test on-access scanning. Anyone quoting you a detection percentage for any engine is selling something; this guide's claim is narrower and provable: the mechanism works, end to end, and now you know exactly what the mechanism is.
