# Installing ClamAV

**Outcome: ClamAV is installed, and two checks prove the programs answer.**

The checks, once installed:

```console
$ clamscan --version
ClamAV 1.5.4

$ freshclam --version
ClamAV 1.5.4
```

Your version may differ — what matters is that both answer. (The lab's exact outputs, including the two complaints below, are in transcript t00.)

## The package install

On Debian and Ubuntu, ClamAV is three packages that together bring the engine's libraries:

```console
$ sudo apt install clamav clamav-daemon clamav-freshclam
```

- `clamav` brings `clamscan` and `sigtool`.
- `clamav-daemon` brings `clamd` and `clamdscan`.
- `clamav-freshclam` brings `freshclam`, set up to run regularly on its own.

Other distributions package the same programs under nearly the same names, and the project publishes builds for Windows and macOS. The concepts in this guide are the same everywhere; only this paragraph changes.

A package install also lays down the furniture the rest of the guide assumes: the database directory `/var/lib/clamav`, configuration files in `/etc/clamav` (`clamd.conf`, `freshclam.conf`), a `clamav` system user that the daemon and the fetcher run as, and services your init system can start. You will meet each of these by name in later chapters.

## The lab's confession

This guide's lab could not run a package manager — its machine's package system was shared with other tenants — so it did the honest equivalent: it downloaded the *same* Ubuntu package files (`clamav`, `clamav-daemon`, `clamav-freshclam`, `libclamav12`, all version 1.5.4) and unpacked them into a private folder, running the genuine binaries from there with hand-written config files pointing into lab folders. Same programs, same version, same behaviour; different furniture. `criteria.md` records the details, including the one system shortcut the lab had to take (a temporary link so the signature verifier could find its certificate).

Two consequences of the lab's arrangement are worth your attention, because a normal install spares you both:

- **The config complaint.** Transcript t00 shows `freshclam --version` and `clamdscan --version` failing with `Can't open/parse the config file /etc/clamav/...` — the lab had no `/etc/clamav` until it wrote configs elsewhere and passed them with `--config-file`. On a package install those files exist from the start, and the bare commands simply work.
- **The missing user.** The lab's first `freshclam` run stopped with `Can't get information about user clamav` — the fetcher drops privileges to the `clamav` user, which the package install creates and the lab did not have. The lab set `DatabaseOwner root` in its own config instead. A normal install never shows you this.

## After installing

Do not scan anything yet. A fresh install's database is whatever was current when the package was built — weeks or months old. The next chapter runs `freshclam` and brings it up to date, and chapter 5's first scan waits until that is done. An antivirus with an old list is a guard reading last season's wanted posters.
