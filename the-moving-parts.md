# The Moving Parts

**Outcome: you can name each ClamAV program, say what it holds, and predict which one a given task needs.**

The check is a question, not a command: when a scan is slow to *start*, which part is responsible? By the end of this chapter you will know it is the database loading — and which program exists to fix exactly that.

## The engine

At the centre is the engine, a library (`libclamav`) that knows how to open files — including the insides of zip archives, disk images, documents with embedded objects, and mail messages — and search their contents for patterns. You never run the library directly. Every tool below is a different doorway into it.

## The signature database

The database is a folder of files, most importantly three:

| File | What it holds |
| --- | --- |
| `main.cvd` | The main signature set — millions of signatures, published as a complete file |
| `daily.cvd` | New signatures published since the last main set — updated every day, as the name says |
| `bytecode.cvd` | A small set of signatures written as little programs, for patterns too fiddly for byte matching |

A `.cvd` file is *signed*: ClamAV can verify it really came from the project before trusting it. On the lab's run, `main.cvd` alone held 3,287,027 signatures in 89 MB, and the three files together gave the engine **3,628,245 known viruses** to check against (transcript t01). Chapter 9 adds two more files of a different kind — hand-written `.ndb` and `.hdb` signatures — which the engine reads from the same folder.

The database lives in one directory (on a package install, `/var/lib/clamav`). Whoever keeps that folder current keeps the scanner honest. That job belongs to —

## freshclam — the fetcher

`freshclam` downloads database updates from the project's mirrors and verifies their signatures before installing them. Run it by hand and it checks each file and reports whether you are current; run as a service, it checks on a schedule. Chapter 4 runs it and reads its report. Its one quirk, proven in transcript t00: `freshclam` reads its config file even to answer `--version`, so it is never quite a bare command.

## clamscan — the scanner you talk to

`clamscan` is the command-line scanner. You name a file or folder; it loads the whole database into memory, scans, prints a verdict per file and a summary, and exits with a code that says how it went (chapter 8).

Loading millions of signatures takes time — about 14 to 17 seconds per run on the lab's shared machine, before a single byte of your file was looked at. For one file, once, that is fine. For a mail server scanning every attachment, it is hopeless. Hence —

## clamd and clamdscan — the daemon pair

`clamd` is a **daemon**: a program that starts once, loads the database, and then *stays running*, listening on a socket. Its log line from the lab says it plainly — started at 09:02:42, `Loaded 3628247 signatures` at 09:02:54, socket open seconds later (transcript t05). The loading cost is paid once, by the daemon, not by you per scan.

`clamdscan` is the client you actually type. It hands `clamd` a file name (or streams the file's contents) and prints the daemon's verdict. It holds no database itself, which is why it answers in moments — and why it fails, with a connection error, when the daemon is not running. Transcript t00 shows it complaining about exactly that before printing its version.

## sigtool — the database's toolbox

`sigtool` inspects database files. `sigtool --info db/main.cvd` reported the lab's main database's version, signature count, build date and that its digital signature verified `OK` (transcript t01). You will rarely need it; when you doubt a database file, it is the tool that settles the doubt.

## Which part holds what

- The **database folder** holds the knowledge.
- The **engine** holds the skill of looking inside files.
- **freshclam** holds the database current.
- **clamscan** holds nothing between runs — it reloads everything, every time.
- **clamd** holds the loaded database, ready; **clamdscan** holds only a question for it.

Keep that map and every later chapter is just one of these parts doing its job. Next: installing the set.
