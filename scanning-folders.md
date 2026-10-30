# Scanning Folders and Quarantine

**Outcome: you can scan a whole folder tree in one command, read its totals, and set a find aside in quarantine instead of destroying it.**

The check: after the folder scan below, the summary's `Scanned files` equals the number of files in the folder, and the one find sits in the quarantine folder as a copy, with the original untouched. Both are from transcript t04.

## The recursive scan

Add `-r` and name a folder instead of a file:

```console
$ clamscan -r testfiles
testfiles/eicar.com: Eicar-Test-Signature FOUND

----------- SCAN SUMMARY -----------
Scanned directories: 1
Scanned files: 5
Infected files: 1
```

`clamscan` walks the folder and everything under it, printing a verdict line for each file that is *found* and — by default — staying quiet about the clean ones in a folder scan's per-file output. The summary carries the totals: directories walked, files scanned, files infected. Five files went in; one was found. If `Scanned files` is lower than you expect, suspect permissions or a path mistake before you suspect the scanner: it reports what it could read, and chapter 8 shows the warning it prints for a file it cannot access.

Folders are where the database-loading cost starts to pay off: the load happens once per run, however many files follow.

## What to do with a find

Out of the box, `clamscan` only *reports*. It changes nothing — a deliberate design this guide approves of. When you want action, you ask for exactly one of these:

| Option | What happens to a found file |
| --- | --- |
| `--copy=DIR` | A copy is placed in `DIR`; the original stays put |
| `--move=DIR` | The file is moved into `DIR`; the original is gone from its place |
| `--remove` | The file is deleted |

The lab used the gentle one:

```console
$ clamscan -r --copy=quarantine testfiles
$ ls quarantine/
eicar.com
```

That folder is a **quarantine**: a place where a suspicious file sits, preserved but out of circulation, while a human decides. Why copy first, rather than delete on sight?

- **False positives exist.** A signature can match a legitimate file (chapter 13). A deleted system file is a broken machine; a quarantined one is a five-minute restore.
- **Evidence matters.** If a file really is malicious, *which* signature matched, and the file itself, are the first things anyone investigating will ask for.
- **Deletion is easy later.** Quarantine loses you nothing but a little disk. Deleting loses you every option but one.

A sane household habit follows: scan with `--copy` (or let the daemon's reports guide you), review quarantine when a scan finds something, delete from quarantine once you are sure — weeks later, not seconds.

## A word about archives

Files inside zip and other archives are opened and scanned as part of the file that holds them; a find is reported against the archive's name. Moving or copying in that case moves the whole archive, not the single bad file inside it — one more reason quarantine beats instant deletion.

You now have the whole one-shot workflow: fresh database, scan, read, set aside. The remaining inefficiency is the wait at the start of every run. The daemon removes it.
