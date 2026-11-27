# OverTheWire Bandit Write-Up: Level 21 → Level 22

## Objective
Retrieve the password for `bandit22` by inspecting scheduled cron jobs in `/etc/cron.d/`, analyzing the script executed as `bandit22`, and reading the temporary output file it generates.

---

## How I Solved It

### 1. Navigated to `/etc/cron.d/` and Listed Scheduled Jobs
After logging into `bandit21` and confirming the home directory was empty, I navigated to `/etc/cron.d/` to inspect the system's scheduled cron jobs:

```bash
cd /etc/cron.d
ls
```
*Output:* `behemoth4_cleanup  clean_tmp  cronjob_bandit22  cronjob_bandit23  cronjob_bandit24  e2scrub_all  leviathan5_cleanup  manpage3_resetpw_job  otw-tmp-dir`

### 2. Inspected the `cronjob_bandit22` Configuration
I read `cronjob_bandit22` to see what command runs and under which user privileges:

```bash
cat cronjob_bandit22
```
*Output:*
```text
@reboot bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
* * * * * bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
```
This revealed that `/usr/bin/cronjob_bandit22.sh` executes every minute (`* * * * *`) as the `bandit22` user.

### 3. Analyzed `/usr/bin/cronjob_bandit22.sh`
Next, I read the shell script to see where it stores its output:

```bash
cat /usr/bin/cronjob_bandit22.sh
```
*Output:*
```bash
#!/bin/bash
chmod 644 /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
cat /etc/bandit_pass/bandit22 > /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
```
The script copies `/etc/bandit_pass/bandit22` into a world-readable (`chmod 644`) file inside `/tmp/`.

### 4. Read the Temporary Password File
Finally, I read the target file in `/tmp/` to retrieve the `bandit22` password:

```bash
cat /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
```

---

## Flag / Password Found
* **Bandit22 Password:** `Rt2rjh5i1X4MLFzuR7VhX6AZQMEZ`

## What I Learned
* How to inspect scheduled tasks in **`/etc/cron.d/`** to identify scripts running automatically under higher-privileged user accounts.
* How weak file permissions (`chmod 644`) in shared directories like **`/tmp/`** can expose sensitive data written by privileged cron jobs.

