# OverTheWire Bandit Walkthrough: Level 13 → Level 14

## Objective
Authenticate as `bandit14` using the SSH private key found in `bandit13` instead of a password, and retrieve the password for `bandit14` from `/etc/bandit_pass/bandit14`.

---

## Step-by-Step Solution

### 1. Save the SSH Private Key Locally
After viewing `sshkey.private` inside `bandit13`, copy the full OpenSSH private key block into a local file named `private.banditkey`:

```bash
nano private.banditkey
# Paste the -----BEGIN OPENSSH PRIVATE KEY----- ... -----END OPENSSH PRIVATE KEY----- block and save
```

Verify the saved key contents:
```bash
cat private.banditkey
```

### 2. Set Strict File Permissions
SSH requires private key files to be protected from access by other users on the system. Restrict permissions to the file owner using `chmod`:

```bash
chmod 700 private.banditkey
```

### 3. Connect to `bandit14` Using the Identity Key
Use the `-i` flag to authenticate with the saved private key file:

```bash
ssh -i private.banditkey bandit14@bandit.labs.overthewire.org -p 2220
```

### 4. Retrieve the `bandit14` Password
Once logged in as `bandit14@bandit`, read the password file stored in `/etc/bandit_pass/`:

```bash
cat /etc/bandit_pass/bandit14
```

---

## Result & Key Commands
* **Bandit14 Password:** `aaWecNkG4FhxJQxz07uiwzVP6bJiYS65`
* **`ssh -i <keyfile>`:** Specifies the private identity file used for public-key authentication.
* **`chmod 700 <file>`:** Locks file permissions so only the owner can read, write, or execute it (preventing SSH's *unprotected private key* warning).

