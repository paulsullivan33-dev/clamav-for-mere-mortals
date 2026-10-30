# ClamAV and Mail

**Outcome: you can describe where a virus scan sits in a mail server's path, what happens to a caught attachment, and why mail is ClamAV's home ground.**

*This chapter is documented-only: the lab ran the scanner and the daemon for real (chapters 5–9), but it did not wire them into a live mail server, and it does not pretend otherwise. The wiring below follows the projects' own documentation. If you run a mail server, the [Postfix & Dovecot guide](https://paulsullivan33-dev.github.io/postfix-and-dovecot-for-mere-mortals/) covers the server itself.*

## Why mail first

Mail is the one place files arrive *uninvited*, all day, from strangers. A scanner that waits for a human to run it is always late there. So mail is where ClamAV is most often found in the wild: not as a program someone launches, but as a stage inside the mail pipeline, examining every attachment before it reaches a mailbox.

## Where the scan sits

A mail message's journey through a typical open source setup:

1. **Postfix** receives the message and hands its contents to a content filter.
2. The filter — commonly **amavisd-new**, or ClamAV's own **clamav-milter** — unpacks the message and its attachments and passes them to **`clamd`**, the daemon from chapter 7, over its socket. The daemon, never `clamscan`: the database is already loaded, and per-message loading would sink the server.
3. `clamd` answers per part: clean, or the name of a signature.
4. The filter acts on the answer by policy: clean mail continues to **Dovecot** and the mailbox; infected mail is **rejected or quarantined** — typically the sender is told the message was refused, and a copy is kept in a virus quarantine for the postmaster's review, never delivered.

Two details in that flow echo earlier chapters. The verdict comes back with a **signature name**, so a refusal can say *what* was caught, and logs can be searched by it. And the daemon scans **as its own user** through a socket — the mail system hands it the message contents, which is why the stream arrangement in chapter 7 exists.

## What it catches there, and what it doesn't

Against mail it earns its keep on the bulk problem: known malicious attachments — the infected invoice, the booby-trapped archive — recognised by signature the moment a signature exists. The phishing-signature part of the database additionally matches known fraudulent message patterns.

It does not read minds or links: a message whose harm lives behind a URL, a brand-new attachment with no signature yet, or plain social engineering in honest text all pass a signature scan cleanly. Mail defence in depth therefore pairs the scanner with spam filtering, attachment-type policies (many servers refuse executable attachment types outright, virus or not), and user sense. ClamAV is one stage of the pipeline, and it is honest work — as long as nobody mistakes it for the whole pipeline.

## If you wire it yourself

The distribution packages carry the pieces (`clamav-milter`, or amavis with its ClamAV integration), and their documented route is: daemon running first (chapter 7's `PONG` is the acceptance test), then the filter pointed at the daemon's socket, then a test message carrying the EICAR string as an attachment — chapter 5's harmless proof, promoted to end-to-end duty. Send it, watch it be refused or quarantined, and read the signature name in the mail log. Until that test passes, the wiring is a drawing, not a defence.
