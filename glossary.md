# Glossary

**Antivirus engine** — The program that examines files and compares them against signatures. In this guide, `libclamav`, reached through `clamscan` or `clamd`.

**Bytecode signatures** — The small part of the database (`bytecode.cvd`) whose signatures are little programs rather than byte patterns, for patterns too fiddly to match directly.

**clamd** — The daemon: starts once, loads the signature database, listens on a socket, and answers scan requests. See chapter 7.

**clamdscan** — The client that hands files (or their contents) to `clamd` and prints its verdicts. Holds no database of its own.

**clamscan** — The one-shot command-line scanner: loads the database, scans what you name, prints verdicts and a summary, exits with a code.

**CVD** — ClamAV Virus Database: the signed file format of the official databases (`main.cvd`, `daily.cvd`, `bytecode.cvd`).

**Daemon** — A program that runs in the background, waiting to be asked for work, rather than starting fresh for each task.

**Detection** — A file matching a signature; reported as the signature's name followed by `FOUND`.

**EICAR test file** — A harmless 68-byte text string the whole antivirus industry agrees to detect as if it were a virus, so scanners can be tested without real malware. Caught as `Eicar-Test-Signature`.

**Exit code** — The number a program leaves behind saying how it went. For `clamscan`: 0 clean, 1 found, 2 error.

**False positive** — A clean file matched by a signature in error. The reason quarantine comes before deletion.

**freshclam** — The updater: downloads, verifies and installs new signature databases from the project's mirrors.

**Hash signature (.hdb)** — A signature consisting of a file's fingerprint (hash and size); matches that exact file and no other.

**Heuristics** — Checks for patterns that *look like* tricks malware uses, rather than for one specific known file's bytes.

**Multiscan** — `clamdscan --multiscan`: the daemon scans a folder's files in parallel rather than one at a time.

**Pattern signature (.ndb)** — A signature describing bytes to find inside a file, with rules for where they may appear.

**PUA** — *Potentially unwanted application*: a category for dual-use tools that are legitimate in some hands; off by default in this guide's runs.

**Quarantine** — A holding folder where found files are copied or moved, preserved but out of circulation, until a human decides.

**Signature** — A written-down recognisable pattern of a known bad file — bytes to match, or a whole-file fingerprint — with a name the report can cite.

**sigtool** — The database toolbox; `sigtool --info` reports a database file's version, signature count and signature verification.

**Socket** — The rendezvous point (here, a file on disk) where a client like `clamdscan` meets its daemon.

**UNOFFICIAL** — The suffix the engine adds to names of signatures from unsigned local files — including yours — marking that they did not come from the signed official database.
