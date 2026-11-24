# OverTheWire Bandit Write-Up: Level 13 → Level 14

## Objective
Log into `bandit14` using the SSH private key provided in `bandit13` instead of a password, and retrieve the password for `bandit14`.

---

## How I Solved It

### 1. Saved the SSH Private Key Locally
After logging into `bandit13` and inspecting `sshkey.private`, I copied the entire OpenSSH private key block onto my local machine and saved it into a file named `private.banditkey`. I then verified that the key was saved properly:

```bash
cat private.banditkey
```

### 2. Secured the Key Permissions
Because SSH rejects private keys with overly open permissions, I locked down access to `private.banditkey` so only my user account could access it:

```bash
chmod 700 private.banditkey
```

### 3. Connected to `bandit14` with the Private Key
Next, I used the `-i` identity flag to authenticate directly into `bandit14` using my local key file:

```bash
ssh -i private.banditkey bandit14@bandit.labs.overthewire.org -p 2220
```

### 4. Retrieved the `bandit14` Password
Once inside the `bandit14` shell, I checked the directory and read the password stored in `/etc/bandit_pass/bandit14`:

```bash
ls
cat /etc/bandit_pass/bandit14
```

---

## Flag / Password Found
* **Bandit14 Password:** `aaWecNkG4FhxJQxz07uiwzVP6bJiYS65`

## What I Learned
* How to authenticate via SSH using a private key file (`ssh -i <keyfile>`) instead of a password.
* Why SSH enforces strict file permissions (`chmod 700` or `chmod 600`) on private keys before allowing a connection.
* Where Bandit stores level passwords (`/etc/bandit_pass/`) once authenticated as the target user.
