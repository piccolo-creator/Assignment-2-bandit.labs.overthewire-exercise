# OverTheWire Bandit Write-Up: Level 16 → Level 17

## Objective
Identify which port in the `31000–32000` range on `localhost` speaks SSL/TLS, submit the current `bandit16` password to retrieve an OpenSSH private key, and use that key to log into `bandit17`.

---

## How I Solved It

### 1. Reviewed the Nmap Manual (`man nmap`)
Before scanning the local port range, I checked the `nmap` manual page (`man nmap`) to review service detection and port-scanning options (`-p`, `-A`) for identifying open services on `localhost`.

### 2. Scanned Ports `31000–32000` on `localhost`
I ran `nmap` across the target port range to find all open ports and determine which ones supported SSL/TLS:

```bash
nmap -p 31000-32000 localhost
```

After identifying the open ports (`31046`, `31518`, `31691`, `31790`, `31960`), I ran service and version detection (`-A`) specifically against them:

```bash
nmap -p 31046,31518,31691,31790,31960 -A localhost
```

* **Scan Findings:**
  * Ports `31046`, `31691`, and `31960` were running plain `echo` services.
  * Port `31518` was running `ssl/echo` (simply echoing back whatever was sent).
  * **Port `31790`** returned `ssl/unknown` with the fingerprint response `"Wrong! Please enter the correct current password."`—confirming it as the target service.

### 3. Submitted the `bandit16` Password via OpenSSL (`-quiet`)
Initially, typing the password manually inside an interactive `openssl s_client` session triggered TLS `KEYUPDATE` commands and risked character typos. To send the password cleanly without interactive interference, I piped `/etc/bandit_pass/bandit16` directly into `openssl s_client` with the `-quiet` flag:

```bash
cat /etc/bandit_pass/bandit16 | openssl s_client -connect localhost:31790 -quiet
```

The service verified the password, responded with `Correct!`, and printed an **OpenSSH Private Key** (`-----BEGIN OPENSSH PRIVATE KEY-----` ... `-----END OPENSSH PRIVATE KEY-----`).

### 4. Saved the Private Key and Logged Into `bandit17`
I saved the returned OpenSSH private key to a temporary file, restricted its permissions with `chmod 600` (or `chmod 700`), and authenticated into `bandit17` using `ssh -i`:

```bash
mkdir /tmp/b17
nano /tmp/b17/bandit17.key
# Pasted the OpenSSH private key block and saved

chmod 600 /tmp/b17/bandit17.key
ssh -i /tmp/b17/bandit17.key bandit17@localhost -p 2220
```

Once connected, my terminal prompt updated to `bandit17@bandit:~$`.

---

## Outcome & Key Takeaways
* **Target Service Found:** `localhost:31790` (SSL/TLS)
* **Access Gained:** Authenticated shell as `bandit17` via OpenSSH private key.
* **What I Learned:**
  * How to use **`nmap -A -p <ports>`** to enumerate open ports and distinguish real application services from `echo` decoys.
  * Why piping input with **`cat <file> | openssl s_client -connect <host>:<port> -quiet`** avoids interactive TLS control characters (such as `R` for renegotiation or `k` for `KEYUPDATE`) and prevents manual transcription errors.
  * How to save an extracted SSH private key, lock down its permissions (`chmod 600`), and pivot to the next user account (`ssh -i`).

