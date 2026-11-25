# OverTheWire Bandit Write-Up: Level 18 → Level 19

## Objective
Retrieve the password for `bandit19` from the `readme` file in the `bandit18` home directory, despite the account's `.bashrc` file being configured to immediately close interactive SSH sessions (`Byebye !`).

---

## How I Solved It

### 1. Observed the Forced Logout on Interactive Login
When logging into `bandit18` normally via SSH, the server displayed the welcome banner, printed `Byebye !`, and immediately terminated the connection (`Connection to bandit.labs.overthewire.org closed.`) because `.bashrc` runs `exit` upon opening an interactive shell.

### 2. Bypassed `.bashrc` by Executing a Remote Command via SSH
Instead of starting an interactive shell, I passed `cat readme` directly at the end of the `ssh` command so the remote server would execute the command non-interactively and return the file contents before closing:

```bash
ssh bandit18@bandit.labs.overthewire.org -p 2220 cat readme
```
*(Note: Passing `-i private.banditkey` fell back cleanly to password authentication when prompted for `bandit18`'s password.)*

After entering the `bandit18` password (`OQxX7JF1ndr98ZuhOTDY8FonT8SZTTXT`), the server printed the contents of `readme` directly in my local terminal.

---

## Flag / Password Found
* **Bandit19 Password:** `KpsOfPKcP7i1FlIExk2QEjyt6dw8dxZI`

## What I Learned
* How **SSH remote command execution (`ssh user@host <command>`)** runs a single command directly on the remote host without spawning a full interactive login shell—bypassing startup scripts like `.bashrc`.
* How to retrieve file contents remotely (`cat readme`) even when interactive terminal access is restricted.

