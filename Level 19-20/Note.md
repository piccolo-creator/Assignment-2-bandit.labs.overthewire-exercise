# OverTheWire Bandit Write-Up: Level 19 → Level 20

## Objective
Retrieve the password for `bandit20` by exploiting a **setuid** binary (`bandit20-do`) in the `bandit19` home directory to execute commands with `bandit20` privileges.

---

## How I Solved It

### 1. Enumerated the File and Checked Permissions
After logging into `bandit19`, I listed the home directory and inspected the `bandit20-do` binary using `file` and `ls -l`:

```bash
ls
file bandit20-do
ls -l bandit20-do
```

* **Findings:**
  * `file bandit20-do` confirmed it was a `setuid ELF 32-bit LSB executable`.
  * `ls -l bandit20-do` showed permissions `-rwsr-x---` owned by `bandit20` and group `bandit19`—meaning any member of `bandit19` can execute it, and it runs with the effective user ID (`euid`) of `bandit20`.

### 2. Tested the Binary's Behavior
Running `./bandit20-do` without arguments printed its usage instructions (`Run a command as another user. Example: ./bandit20-do whoami`). I verified that commands passed to it executed under `bandit20` privileges:

```bash
./bandit20-do id
# Output: uid=11019(bandit19) gid=11019(bandit19) euid=11020(bandit20) groups=11019(bandit19)

./bandit20-do whoami
# Output: bandit20
```

### 3. Read the `bandit20` Password File
Because `./bandit20-do` executes commands with `euid=11020(bandit20)`, I used it to read `/etc/bandit_pass/bandit20`:

```bash
./bandit20-do cat /etc/bandit_pass/bandit20
```

---

## Flag / Password Found
* **Bandit20 Password:** `4pTjcunZ0fK2vmpJTwfG8Vf7VhxDeg0A`

## What I Learned
* How **SUID (`setuid`) permissions (`-rwsr-x---`)** allow an executable to run with the file owner's effective user ID (`euid`) rather than the caller's UID.
* How to inspect unknown binaries safely using `file` and `ls -l` before executing them.
* How privileged wrapper binaries can be used to read restricted files (`/etc/bandit_pass/bandit20`) when commands are passed as arguments.

