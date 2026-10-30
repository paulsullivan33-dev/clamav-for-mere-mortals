# Reading the Results

**Outcome: given any ClamAV output, you can say which of the three endings it was — clean, found, or went wrong — and you trust exit codes over impressions.**

The check: a scan of a file that does not exist prints a summary saying nothing was scanned and *still* looks tidy. By the end of this chapter you will read its exit code first and know it failed. Every case here is real (transcripts t02 and t06).

## The three endings

Every file verdict is one of two lines:

```text
path/to/file: OK
path/to/file: Eicar-Test-Signature FOUND
```

and every *run* ends in one of three states, which the exit code states exactly:

| Exit code | Meaning | What happened |
| --- | --- | --- |
| `0` | Clean | Every file was scanned; nothing matched |
| `1` | Found | The scan worked, and at least one file matched a signature |
| `2` | Error | Something went wrong — a file unreadable, a database missing or malformed |

Scripts live on this table: `clamscan ... && echo all clean` does the right thing because a find exits 1 and an error exits 2, so neither masquerades as success. The lab's transcripts echo the code after each scan so you can watch the table hold: 0, 1, 0 across chapter 5's three files.

`clamdscan` plays by almost the same rules, with one extra observed value: asked to ping a daemon that was not there, it exited **21** — its way of saying the failure was in reaching the daemon at all, before any scanning could be discussed (transcript t05).

## The summary block, line by line

```text
----------- SCAN SUMMARY -----------
Known viruses: 3628245      <- signatures actually loaded; your freshness proof
Engine version: 1.5.4
Scanned directories: 1
Scanned files: 5            <- files the engine really examined
Infected files: 1           <- files that matched
Data scanned: ...           <- bytes examined, archives counted from inside
Time: 13.979 sec
```

Two lines do most of the diagnostic work in practice. `Known viruses` tells you whether the database loaded at all (chapter 4's number, checked again for free), and `Scanned files` tells you whether the scan covered what you think it covered. A backup job that reports `Scanned files: 0` has scanned nothing, however reassuring its layout.

## Errors that look like verdicts

Transcript t06 keeps the three failures that teach the most:

**A file that is not there.**

```text
testfiles/no-such-file.txt: No such file or directory
WARNING: testfiles/no-such-file.txt: Can't access file
Scanned files: 0
exit code: 2
```

The run continues, prints a normal-looking summary, and only the `Scanned files: 0`, the warning, and the exit code confess. In a folder scan, the same warning appears for files the scanner's user cannot read — `clamd`'s permission trap from chapter 7 announces itself exactly this way. *Warnings are results.* Skim for them.

**An empty database folder.**

```text
LibClamAV Error: cli_loaddbdir: No supported database files found in ...
ERROR: Can't open file or directory
exit code: 2
```

This is the brand-new-install mistake: scanning before the first `freshclam`. The scanner refuses rather than scanning with no knowledge — a good refusal, and the reason chapter 4 comes before chapter 5.

**A malformed signature file.**

```text
LibClamAV Error: Problem parsing database at line 1
LibClamAV Error: Can't load .../bad.ndb: Malformed database
ERROR: Malformed database
exit code: 2
```

One broken hand-written signature (chapter 9 writes them, so chapter 9 can break them) stops the whole load. The engine parses database files strictly, names the offending file and line, and scans nothing until it is fixed. Strictness here is a feature: a half-loaded database would be a scanner lying by omission.

## The habit

Read in this order: **exit code, warnings, summary counts, verdict lines.** That order turns any ClamAV output — yours, a cron job's, a mail server's — into a straight answer: it worked or it didn't; it covered what you asked; and here is what it found.
