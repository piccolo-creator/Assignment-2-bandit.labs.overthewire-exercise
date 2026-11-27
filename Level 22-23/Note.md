# OverTheWire Bandit Write-Up: Level 22 → Level 23

## Objective
Retrieve the password for `bandit23` by analyzing the scheduled cron job script `/usr/bin/cronjob_bandit23.sh`, replicating its MD5 hashing logic to determine the target filename in `/tmp/`, and reading the password file.

---

## How I Solved It

### 1. Inspected `/etc/cron.d/` for `bandit23` Tasks
After logging into `bandit22`, I navigated to `/etc/cron.d` and checked the `cronjob_bandit23` configuration:

```bash
cd /etc/cron.d
ls
cat cronjob_bandit23
```
*Output:*
```text
@reboot bandit23 /usr/bin/cronjob_bandit23.sh &> /dev/null
* * * * * bandit23 /usr/bin/cronjob_bandit23.sh &> /dev/null
```
This confirmed that `/usr/bin/cronjob_bandit23.sh` runs every minute under the `bandit23` user account.

### 2. Analyzed `/usr/bin/cronjob_bandit23.sh`
Next, I read the shell script to see how it generates the output file path:

```bash
cat /usr/bin/cronjob_bandit23.sh
```
*Output:*
```bash
#!/bin/bash

myname=\$(whoami)
mytarget=(echo I am user myname | md5sum | cut -d ' ' -f 1)

echo "Copying passwordfile /etc/bandit_pass/\$myname to /tmp/\$mytarget"

cat /etc/bandit_pass/myname > /tmp/mytarget
```
When executed by the `bandit23` cron job, `$myname` evaluates to `bandit23`, and `$mytarget` is the MD5 hash of the string `"I am user bandit23"`.

### 3. Calculated the Target MD5 Filename
I ran the exact same command in my terminal, substituting `bandit23` for `$myname`, to calculate the filename in `/tmp/`:

```bash
echo I am user bandit23 | md5sum | cut -d ' ' -f 1
```
*Output:* `8ca319486bfbbc3663ea0fbe81326349`

### 4. Read the Password from `/tmp/`
Finally, I read `/tmp/8ca319486bfbbc3663ea0fbe81326349` to retrieve the `bandit23` password and exited the session:

```bash
cat /tmp/8ca319486bfbbc3663ea0fbe81326349
```

---

## Flag / Password Found
* **Bandit23 Password:** `gKY9laXnIz30BxiPjR2Z0quttlPZR8sw`

## What I Learned
* How to reverse-engineer dynamic file naming logic in shell scripts (`$(echo I am user $myname | md5sum | cut -d ' ' -f 1)`).
* Why relying on predictable MD5 hashes ("security through obscurity") in shared directories like `/tmp/` does not protect sensitive files from being located and read.

