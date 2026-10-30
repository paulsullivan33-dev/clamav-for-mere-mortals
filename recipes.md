# Recipes

**Outcome: four working habits, each assembled from parts the earlier chapters verified.**

These recipes combine proven pieces — fresh database checks, `clamscan` exit codes, quarantine copies, the daemon's ping. The scheduling and service plumbing around them follow your system's usual tools.

## 1. The nightly scan

Scan what matters, while nobody is waiting:

```console
$ freshclam --quiet
$ clamscan -r --copy=/var/quarantine/home /home; echo "exit code: $?"
```

Run it from `cron` or a systemd timer in the small hours. Read the result the way chapter 8 taught: exit `0` and a plausible `Scanned files` count means a real, clean pass; exit `1` means look in quarantine and at the verdict lines; exit `2` means the scan itself failed and tonight's "all clear" is fiction. A one-line wrapper that mails or messages you *only* on exit 1 or 2 turns this from a log nobody reads into an alarm that means something.

## 2. Scan a download before opening it

One file, one command, using the daemon if it is running:

```console
$ clamdscan ~/Downloads/installer-from-a-forum.zip
```

or `clamscan` with the same path when it is not. The point is the habit's placement: *before* the double-click, not after the weird behaviour starts. Pair it with chapter 13's honesty: clean means "no known signature matched", so the file's source still matters.

## 3. The removable-drive ritual

USB sticks are files arriving uninvited, like attachments with legs:

```console
$ clamscan -r --copy=/var/quarantine/usb /media/you/USBSTICK
```

Scan on arrival, before copying anything off it. If the stick came from a machine you suspect, do this from a machine you can afford to be careful on, and treat a find in quarantine as a story to investigate, not a file to forgive.

## 4. Keep your own ban list

The organisation-wide junk attachment, the broken installer that keeps coming back — chapter 9's hash signature ends it:

```console
$ md5sum bad-attachment.bin
$ wc -c bad-attachment.bin
```

Write the `hash:size:Your.Name` line into your own `.hdb` file in the database folder, reload the daemon (or let freshclam's notification do it on the next update), and that exact file is refused everywhere your scanner stands — including, if chapter 10's wiring is yours, at the mail gate. Test the signature the chapter 9 way first: a two-signature folder, a known file, a verdict with your name on it.
