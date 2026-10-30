# Your Own Signatures

**Outcome: you have written two signatures of your own — one for a pattern, one for a whole file — and watched the engine catch files with them, by name.**

The check is transcript t03: with *only* the lab's two signatures loaded, the summary says `Known viruses: 2`, and both invented files are found under the lab's own names. No real malware is involved anywhere in this chapter; that is the point of it.

## Why write your own?

Not to compete with the project's 3.6 million. Your own signatures serve smaller, realer purposes:

- **Proof.** Writing one is the fastest way to understand what a signature *is* — the mystery leaves the subject entirely.
- **Local bans.** A specific unwanted file is circulating in *your* world — a junk attachment your organisation keeps receiving, a broken update you never want installed. One hash signature ends it locally, today, without waiting for the world.
- **Tests.** A signature for a marker string is a safe stand-in for a virus in any pipeline test — the EICAR idea, generalised.

## Signature one: a pattern (.ndb)

The lab invented a marker string — `LABTEST-MARKER-7f3a9c1e-DO-NOT-SHIP` — and put it inside an otherwise ordinary report file. The signature that hunts the marker is one line in a file named `labtest.ndb`, in a database folder:

```text
Lab.Sig.MarkerTest:0:*:<the marker, written in hexadecimal>
```

Read it left to right, fields separated by colons:

1. **The name** — `Lab.Sig.MarkerTest`. Yours to choose; make it say whose it is.
2. **Target `0`** — what kind of file to look in; `0` means any file.
3. **Offset `*`** — where in the file to look; `*` means anywhere.
4. **The pattern, in hex** — every byte of the marker written as two hex digits. `sigtool` and any hex editor will show you a file's bytes; chapter 2's engine does the matching.

## Signature two: a whole file (.hdb)

The second kind does not look inside at all. It fingerprints one exact file — its MD5 hash and its size — in `labtest.hdb`:

```text
<md5 of the file>:<size in bytes>:Lab.Sig.WholeFile
```

Change one byte of that file and the fingerprint no longer matches. A hash signature is a wanted poster for one specific individual, useless against its edited cousins — which is precisely its honest use: banning *this exact file*.

## The proof

With a database folder containing only these two files (transcript t03):

```console
$ clamscan --database=db-custom testfiles/marked-report.txt
testfiles/marked-report.txt: Lab.Sig.MarkerTest.UNOFFICIAL FOUND

$ clamscan --database=db-custom testfiles/flagged-by-hash.txt
testfiles/flagged-by-hash.txt: Lab.Sig.WholeFile.UNOFFICIAL FOUND

$ clamscan --database=db-custom testfiles/notes.txt
testfiles/notes.txt: OK

$ clamscan --database=db-custom testfiles/eicar.com
testfiles/eicar.com: OK
```

Four verdicts, each teaching something:

- Both inventions are caught, **by the names the lab gave them** — the full engine path, from hand-written text file to named detection, works exactly as the official database works.
- The engine appends **`.UNOFFICIAL`** to names from unsigned local files. That suffix is a trust label: these signatures were not verified against the project's signing key, and the engine says so on every report. Official detections never carry it.
- The clean file stays clean: a two-signature database knows two things.
- And EICAR — a *real* official signature's target — scans **OK** here, because this little database does not contain its signature. A scanner knows what its database knows. Chapter 1's model, demonstrated from the other side.

## House rules for your own signatures

- Keep them in their own file, clearly named, in the database folder (or a folder of your own that you pass with `--database`). The daemon reads the same folder, so `clamd` picks them up on its next reload.
- A malformed line stops the whole database loading (chapter 8 kept the error verbatim). Test a new signature against `clamscan` with only your folder loaded, exactly as the lab did, before it joins the real database.
- Expect the `.UNOFFICIAL` suffix; do not try to disguise your signatures as official ones.

That is the entire trick of antivirus, performed by hand: a pattern, a name, a match, a report. The official database is this, at industrial scale and with a signing key.
