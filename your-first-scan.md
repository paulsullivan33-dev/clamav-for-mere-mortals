# Your First Scan

**Outcome: you have scanned three files — a clean one, the industry's test file, and a file that merely talks about viruses — and read all three verdicts correctly.**

The check is the third scan. If you can say why the essay is *clean* while the test file is *found*, the chapter has done its work. All three scans are real, from transcript t02.

## One clean file

Any ordinary file will do. The lab used a page of meeting notes:

```console
$ clamscan testfiles/notes.txt
testfiles/notes.txt: OK

----------- SCAN SUMMARY -----------
Known viruses: 3628245
Engine version: 1.5.4
Scanned files: 1
Infected files: 0
Time: 17.307 sec (0 m 17 s)
```

The verdict is the first line: the file's name, then `OK`. Below it, every scan prints a **summary block**: how many signatures were loaded (`Known viruses` — your freshness check from chapter 4), how many files were scanned, how many were infected, and how long it took. Note the time: nearly all of those 17 seconds were spent loading the database on the lab's busy shared machine, not scanning a 90-byte file. Remember that when chapter 7 arrives.

`clamscan` with no options uses the default database directory. (The lab's transcripts pass `--database=...` because its database lived in a lab folder; a package install does not need it.)

## The test file, caught

Now the proof. The **EICAR test file** is a 68-byte text string that the whole antivirus industry has agreed to detect as if it were a virus. It is not a virus: it cannot run, spread, or harm anything — create it exactly like this, with no trailing newline:

```console
$ printf 'X5O!P%%@AP[4\\PZX54(P^)7CC)7}$EICAR-STANDARD-ANTIVIRUS-TEST-FILE!$H+H*' > eicar.com
$ wc -c eicar.com
68 eicar.com
```

(The `%%` and `\\` are `printf` escaping its own `%` and `\`; in a text editor, type the string with single characters and save it with no final newline.)

Scan it:

```console
$ clamscan eicar.com
eicar.com: Eicar-Test-Signature FOUND
...
Infected files: 1
```

`FOUND` is the other verdict. Before the word comes the **signature's name** — `Eicar-Test-Signature` — because a detection is not just an alarm, it is an identification: this file matched *that* known pattern. Everything chapter 8 teaches about reading results builds on this line.

## The file that only talks

Last, a teaching negative. The lab wrote a short school essay *about* viruses — the word appears several times:

```console
$ clamscan testfiles/essay-about-viruses.txt
testfiles/essay-about-viruses.txt: OK
Infected files: 0
```

Clean, and correctly so. Signatures match the *bytes of known bad files*, not words, topics or intentions. A scanner that flagged every document mentioning viruses would flag its own manual. This is worth internalising early, because the opposite error is just as common: assuming a file *about* something safe is therefore safe. The essay is clean because its bytes match nothing on the list — full stop.

## What you now know

- `OK` means no loaded signature matched this file.
- `Name FOUND` means a match, and the name says which.
- The summary block reports what the engine loaded and did.
- And quietly, in each transcript, the exit codes agreed: 0 for the clean files, 1 for the find. Chapter 8 makes that official.

Next: whole folders at once, and what to do with a find other than delete it.
