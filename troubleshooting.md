# Troubleshooting

**Outcome: the five failures this work actually produces — plus two from the lab's own setup — each with its verbatim words and its fix.**

Read errors the way chapter 8 taught: exit code first, then the words. Everything quoted below was really printed (transcripts t00, t05, t06).

## "Could not connect to clamd on LocalSocket …"

```text
ERROR: Could not connect to clamd on LocalSocket .../clamd.sock: No such file or directory
exit code: 21
```

`clamdscan` is a client with nothing to talk to: the daemon is not running, or the socket path in the config differs between daemon and client. Start the daemon (`systemctl start clamav-daemon`), then prove it with `clamdscan --ping` and expect `PONG`. Note that transcript t00 shows this same error from `clamdscan --version` — the client checks the daemon even to introduce itself.

## "Can't access file" warnings in an otherwise fine scan

The scanner — usually the daemon, running as the `clamav` user — could not read that file. `Scanned files` quietly shrinks while everything else looks normal. Fix the permissions, or use `clamdscan --stream`/`--fdpass` so the contents are handed over instead of the path. Treat any `Can't access` line as a hole in the scan, because it is one.

## "No supported database files found"

```text
LibClamAV Error: cli_loaddbdir: No supported database files found in ...
exit code: 2
```

The scanner found an empty (or wrong) database folder. Almost always: a fresh install that has not yet run `freshclam`, or a `--database` path pointing somewhere unintended. Run `freshclam` (chapter 4), confirm the three `.cvd` files exist in the database directory, and scan again.

## "Malformed database"

```text
LibClamAV Error: Can't load .../bad.ndb: Malformed database
exit code: 2
```

One signature file is broken, and the whole load is refused — strictly, and on purpose (chapter 8). The message names the file and, above it, the line. If you have been writing your own signatures (chapter 9), this is your typo announcing itself: correct the line's field count and colons, and test in a private database folder before returning the file to the real one.

## freshclam "up-to-date" but the scan's count never changes

`Known viruses` in the scan summary is the ground truth of what the engine loaded. If it stalls while `freshclam` reports success, suspect: `freshclam` writing to a different database directory than the scanner reads (compare `DatabaseDirectory` in both configs), or a daemon that has not reloaded since the update (restart it, or check its log for the reload line). The lab's sandbox added one more flavour — a DNS warning and fallback to HTTP — which was noise, not failure; chapter 4 separates the two.

## The lab's own two traps (a normal install spares you both)

- **`Can't get information about user clamav`** — freshclam dropping privileges to a user that does not exist. The package install creates that user; the lab's extracted tree did not, and its config set `DatabaseOwner root` instead. If you ever see it, your install is missing the user, not a password.
- **`Can't open/parse the config file /etc/clamav/...`** from `--version` — freshclam and clamdscan read their configs even for that. In the lab the files lived elsewhere and were passed with `--config-file`. If a package install shows you this, the config file itself is missing or unreadable, and now you know why a mere version check cares.

## Still stuck

Read the summary counts before the prose, and quote the error *exactly* when you search or ask — these messages are specific, and `Problem parsing database at line 1` and `Malformed database` are different doors into different rooms.
