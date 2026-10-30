# Writing criteria for this guide

Every "for Mere Mortals" guide is written to the same nine-point
standard, so a reader who trusts one can trust the rest. Here it
is, with the verification notes for this particular guide below.

1. **Fix it first.** Reader outcomes lead, not action items: each
   chapter opens by naming the outcome it delivers, shows a check
   the reader can run to confirm they got it, and a reasonable
   reader finishes able to state what was done, what it means,
   and what to try next.
2. **Plain language.** Short sentences, everyday vocabulary,
   technical terms defined on first use. No slang, and no
   idioms that only make sense in one country — the reader may
   be translating as they go.
3. **No dead ends.** A guide never tells the reader to do
   something it cannot show them how to verify. Every claim a
   reader relies on — an install, a command, an output — is
   something this guide demonstrates or clearly labels
   documented-only below.
4. **Live-verified where it matters.** Procedures are run for
   real in the guide's lab wherever the build machine allows it,
   and the transcripts kept in the repository are the evidence.
   What could not be run is listed honestly below, never
   presented as if it had been.
5. **General audiences.** Nothing in this guide — examples,
   fixtures, names, addresses — is modelled on any particular
   reader or author's real life, network, or organisations.
   The lab's files are meeting notes, a school essay, and files
   invented to carry a marker string.
6. **Standalone.** A reader needs this guide, its lab, and
   nothing else. Where another guide would genuinely help, it
   is pointed to once, by name, and the chapter still completes
   without it.
7. **Honest about limits.** Chapter 13 names what a signature
   scanner cannot do: it knows only published patterns, a clean
   verdict is not a safety promise, matches can be wrong, it
   scans files at rest rather than behaviour, removal and
   incident response are not its job, and no detection rate
   against real malware is claimed anywhere in the guide.
8. **The ethics rule is in the text, early.** Chapter 1 and
   the README state the rule before any procedure: scan your
   own machines and files you have the right to scan, and
   never fetch real malware to test with. The guide's proofs
   use only the EICAR standard test file and signatures the lab
   wrote for its own invented files; no real malicious file was
   downloaded, created, or handled at any point.
9. **Builds clean.** The site builds with MkDocs in strict mode
   with no warnings, and a fresh reader following the chapters
   in order reaches the stated outcome of each.

## Verification notes for this guide

The build machine is a 2-CPU, 7.7 GB Ubuntu 24.04 sandbox,
shared with other work while the lab ran (timings are therefore
ranges, and the guide says so). Its package system was shared
with other tenants, so the lab downloaded the genuine Ubuntu
package files — `clamav`, `clamav-daemon`, `clamav-freshclam`
and `libclamav12`, all **1.5.4** from noble-updates — and
unpacked them, unmodified, into a private tree, running the
binaries from there with hand-written configuration files
(kept in `clamav-lab/`). One system shortcut was needed: a
temporary `/etc/clamav` link to the tree so the signature
verifier could find its certificate directory; it was removed
when the lab finished. The sandbox's DNS is intercepted, which
is why transcript t01 shows freshclam's "Invalid DNS reply"
warning and its fallback to HTTPS — a sandbox artefact, called
out in chapter 4. All transcripts live in
`clamav-lab/transcripts/`.

Run for real, with transcripts as evidence:

- Versions of all five tools, including the two honest
  complaints: `freshclam` and `clamdscan` read their config
  files even for `--version`, and in the lab those files did
  not exist at the compiled-in path until passed explicitly
  (t00).
- freshclam fetching the official database from
  database.clamav.net: main.cvd version 63 (3,287,027
  signatures, 89 MB), daily.cvd version 28169 (355,841
  signatures), bytecode.cvd version 339 — each tested by
  freshclam before installation, and `sigtool --info`
  confirming main.cvd's own digital signature verifies (t01).
  The first download ran during lab setup; its output is
  appended to t01 verbatim and labelled as such, and the
  recorded re-run shows all three databases up to date.
- First scans against the official database (3,628,245 known
  viruses loaded): an invented notes file `OK`, exit 0; the
  EICAR test file (written by the lab, exactly 68 bytes)
  reported `Eicar-Test-Signature FOUND`, exit 1; an essay
  that mentions viruses several times `OK`, exit 0 (t02).
  Each clamscan run's 13–17 seconds was almost all database
  loading on the shared machine — the fact chapter 7 exists
  because of.
- The lab's own signatures, alone in a database folder
  (`Known viruses: 2`): a pattern signature (.ndb) for an
  invented marker string and a whole-file hash signature
  (.hdb), both kept in `clamav-lab/`. Each caught its file
  under the lab's name with the engine's `.UNOFFICIAL` suffix;
  a clean file stayed clean, and the EICAR file scanned `OK`
  under this database because its signature was not in it
  (t03).
- A recursive folder scan (5 files, 1 infected) and the same
  scan with `--copy` placing the find in a quarantine folder,
  original untouched (t04).
- clamd end to end: started with the lab config, loaded
  3,628,247 signatures (the official set plus the lab's two,
  copied into the database folder first and disclosed in the
  transcript), answered `PONG` to a ping, scanned clean and
  found files over its socket, and ran a multiscan of the
  test folder that found all three detectable files. After
  the daemon was stopped, a further ping failed with the
  connection error and exit code 21, both kept (t05, with the
  daemon's startup log appended).
- The failures, verbatim: scanning a file that does not
  exist (warnings, `Scanned files: 0`, exit 2); scanning
  with an empty database folder (`No supported database
  files found`, exit 2); a deliberately malformed signature
  file refusing the whole load (`Malformed database`,
  exit 2) (t06).

Documented only — described from the projects' documentation,
not run, and the text does not claim otherwise:

- The `apt install` route itself and everything it lays down
  (services, the `clamav` user, `/etc/clamav` furniture). The
  same packages' binaries and behaviour were run from the
  extracted tree; the two places where that difference shows
  (the missing user, the config-path complaints) are kept in
  the transcripts and explained in chapters 3 and 14.
- freshclam and clamd running as system services under
  systemd, including freshclam's notify-the-daemon step: the
  lab ran both by hand because its machine has no service
  manager to register with.
- Mail integration (chapter 10 entire): amavisd-new and
  clamav-milter wiring into Postfix, rejection and virus
  quarantine policy. No mail server was run.
- On-access scanning (clamonacc), `clamdscan --stream` and
  `--fdpass` in anger, clamsubmit, and the Windows and macOS
  builds.
- Detection rates against real malware — not measured, by
  design and by rule (criterion 8). The guide proves the
  mechanism, never a percentage.
- The neighbour tools of chapter 12 (Defender, commercial
  suites, VirusTotal, YARA, rkhunter) — characterised, not
  run or benchmarked.

Fixtures are this guide's own and deliberately generic:
meeting notes, a school essay about viruses, the EICAR
standard test string, and two invented files built to be
caught by the lab's own signatures. No real malware, no real
people, no real organisations appear anywhere in the lab.
