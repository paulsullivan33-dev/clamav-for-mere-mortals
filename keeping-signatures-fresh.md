# Keeping Signatures Fresh

**Outcome: your signature database is current, and you can prove it from freshclam's own report.**

The check is in the output itself. A healthy run ends with each database reported up to date, or updated — never with an error you scrolled past:

```console
daily.cvd database is up-to-date (version: 28169, sigs: 355841, ...)
main.cvd database is up-to-date (version: 63, sigs: 3287027, ...)
bytecode.cvd database is up-to-date (version: 339, sigs: 80, ...)
```

Those are the lab's real numbers from transcript t01.

## Running freshclam

```console
$ sudo freshclam
```

`freshclam` reads `/etc/clamav/freshclam.conf`, which names the database directory and the mirror to fetch from (`database.clamav.net`, the project's round-robin mirror service). For each of the three database files it fetches a small header first, compares versions, and downloads only what has changed. When a new edition arrives it does three things in order: downloads it, *tests* it (the lab's first run printed `Database test passed` for each file), and only then installs it — and since the files are digitally signed, a tampered or corrupted download fails verification instead of reaching your scanner.

On the lab's first run the downloads were the full files — 89 MB of `main.cvd`, 23 MB of `daily.cvd`, a quarter of a megabyte of `bytecode.cvd` — because there was nothing to update *from*. Later runs move only the changed parts.

## Reading the report

Each line names a file, its version, and how many signatures it carries:

- `daily.cvd` is where the action is: new signatures for new malware appear here first, every day. Its version number climbs constantly (28,169 on the lab's run).
- `main.cvd` changes rarely — a new edition folds the accumulated daily signatures into the main set (version 63, with its 3.3 million signatures).
- `bytecode.cvd` is tiny and changes rarest of all: 80 signatures that are small programs rather than byte patterns.

When `freshclam` says everything is up to date, believe it but verify occasionally: the summary your next scan prints — `Known viruses: 3628245` — is the engine counting what it actually loaded. If that number stalls for weeks while `freshclam` claims success, something is wrong; chapter 14 has the traps.

## Keeping it fresh without you

A package install sets `freshclam` up as a service or timer that checks several times a day, which is the right arrangement: signature freshness is not a chore to remember, it is a property the machine should have. The lab ran `freshclam` by hand because its machine had no service manager to register with — the transcripts show the manual form, and the service differs only in who types the command (nobody).

One courtesy rule the whole internet depends on: `freshclam`'s mirrors are a shared, donated resource. Checking a few times a day is welcome; hammering them every minute from thousands of machines is how mirrors get switched off. The packaged schedule already gets this right — leave it alone.

## When the network says no

The lab's sandbox intercepts DNS in its own way, so its report carries a line a normal machine will not show: `WARNING: Invalid DNS reply. Falling back to HTTP mode.` freshclam prefers a DNS trick to learn whether updates exist; when that failed, it fell back and fetched over HTTPS anyway. The lesson is the general one: a *warning* inside an otherwise successful report is information, not failure. An *error* is different — no files updated, and your database quietly aging. Chapter 14 separates the two.
