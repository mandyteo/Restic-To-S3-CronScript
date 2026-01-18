# Restic Automated S3 Backup for Linux Home Folders

Personal Project of a systemd-based automation script for **incremental, snapshot-based restic backups of Linux home directories to an S3-compatible storage endpoint**. Relies on [Restic](https://restic.net/) software.

## Quick Start

### 1. Clone this repo

```bash
git clone https://github.com/mandyteo/Restic-S3-Linux-Home-Backup.git
cd Restic-S3-Linux-Home-Backup
```

### 2. Install restic

See Restic Documentation on [Installation](https://restic.net/#installation).

### 3. Set up secrets

```bash
cp shayu-home.sample.env /etc/restic/shayu-home.env
sudo chmod 600 /etc/restic/shayu-home.env
```

> Fill `/etc/restic/shayu-home.env` with your S3 and API credentials

### 4. Backup excludes

```bash
cp home-excludes.txt home-excludes.txt
```

> Edit `/etc/restic/home-excludes.txt` as needed. By default, I skipped browser profiles, caches, Steam games and some of my personal files.

### 5. Backup script

```bash
sudo cp restic-home-backup.sh /usr/local/sbin/
sudo chmod 750 /usr/local/sbin/restic-home-backup.sh
```

> See the last command execution on `/usr/local/sbin/restic-home-backup.sh`. By default, I keep 14 daily, 8 weekly, and 12 monthly snapshots with pruning of old data.

### 6. Enable systemd service and timer

```bash
sudo cp restic-home-backup.service /etc/systemd/system/
sudo cp restic-home-backup.timer /etc/systemd/system/
sudo systemctl daemon-reload
sudo systemctl enable --now restic-home-backup.timer
```

> Modify the timer as you see fit. By default, I set it to run daily (24 hours).
>
> You can view its run logs in Journal: `journalctl -u restic-home-backup.service --no-pager`

## Test Dry Run & Verbosity Configuration

For dry run:

```bash
sudo DRYRUN=1 /usr/local/sbin/restic-home-backup.sh
```

To see detailed output:

```bash
sudo VERBOSE=1 /usr/local/sbin/restic-home-backup.sh
```

## Restore Example

To restore the latest snapshot:

```bash
sudo -E bash -c 'set -a; source /etc/restic/shayu-home.env; set +a; restic restore latest --target /tmp/restored-home'
```

> Restores to `/tmp/restored-home`, edit the `--target` accordingly. I personally prefer to manually audit through and copy over required files.
