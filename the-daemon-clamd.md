# The Daemon — clamd

**Outcome: the daemon is running, answers a ping, and scans files through its socket — with no database load in sight.**

The check is `PONG`. When the daemon is up and reachable, its client can ask whether it is alive, and it answers in one word. Everything in this chapter is real, from transcript t05.

## Why a daemon at all

Every `clamscan` run in the earlier chapters paid the same entrance fee: load 3.6 million signatures, then scan. On the lab's machine that fee was 14 to 17 seconds — for a 90-byte file. A mail server doing that per message would drown.

`clamd` pays once. It starts, reads the database, and waits. The lab's log tells the story with timestamps (transcript t05, appendix):

```text
09:02:42 -> +++ Started
09:02:54 -> Loaded 3628247 signatures.
09:02:58 -> LOCAL: Unix socket file .../run/clamd.sock
```

Twelve seconds of loading, once — and then every scan is just the scan.

## Starting it, and the ping

On a package install the daemon is a service:

```console
$ sudo systemctl start clamav-daemon
```

(The lab, having no service manager, started `clamd` directly with its lab config file; the program is the same.) It listens on a **socket** — on Debian/Ubuntu, `/var/run/clamav/clamd.ctl` by default — and `clamdscan` is how you speak to it. First, prove it is alive:

```console
$ clamdscan --ping 2
PONG
```

`PONG` is the whole health check profession in one word: the daemon is up, the socket is right, and you may proceed. When it is *not* up, the same command fails with `Could not connect to clamd on LocalSocket ...` — transcript t05 ends with exactly that error after the daemon is stopped, and chapter 14 starts there.

## Scanning through the socket

```console
$ clamdscan testfiles/notes.txt
.../testfiles/notes.txt: OK

$ clamdscan testfiles/eicar.com
.../testfiles/eicar.com: Eicar-Test-Signature FOUND
```

Same verdicts, same names, a fraction of the wait. Two differences from `clamscan` are worth naming:

- **Paths come back absolute.** The daemon reports the full path it was given or resolved — in the transcripts, the lab's files are reported under their complete `/home/...` names.
- **The daemon scans as its own user.** `clamd` runs as the `clamav` user, so it can only read files that user may read. A scan of *your* home folder by the daemon can silently skip what it cannot open — the classic clamd trap, and chapter 14's first entries. (`clamdscan` has `--fdpass` and `--stream` modes that hand the daemon the file's *contents* instead of its name, sidestepping the permission question; the mail world uses the stream form.)

## Many files at once — multiscan

Hand `clamdscan` a folder with `--multiscan` and the daemon scans its files **in parallel**, one thread per file, instead of in single file:

```console
$ clamdscan --multiscan testfiles
.../eicar.com: Eicar-Test-Signature FOUND
.../marked-report.txt: Lab.Sig.MarkerTest.UNOFFICIAL FOUND
.../flagged-by-hash.txt: Lab.Sig.WholeFile.UNOFFICIAL FOUND
Infected files: 3
```

Wait — three? The folder held the test file plus the lab's two invented files from chapter 9, and before this run the lab had copied its two hand-written signatures into the main database folder, so the daemon knew them. That is the transcripts being honest about their own setup, and it demonstrates something real: the daemon and `clamscan` read the *same* database folder, so a signature added there serves both. It also demonstrates the daemon reloading question — a daemon learns about new signatures when it reloads its database, which `freshclam` arranges by notifying it after an update (the service setup does this for you; the lab, updating by hand with the daemon stopped, loaded them at start).

## Stopping, and what the pieces are for

Stop the service the way you started it (`systemctl stop clamav-daemon`); the lab simply signalled its daemon's process, whose log closed with `Pid file removed` and `Socket file removed` — a tidy daemon leaves no furniture behind.

Rule of thumb for choosing: **occasional scans by hand — `clamscan`. Anything frequent, scheduled, streamed or served — `clamd` with `clamdscan`.** The mail chapter's setup is the daemon's, for exactly this reason.
