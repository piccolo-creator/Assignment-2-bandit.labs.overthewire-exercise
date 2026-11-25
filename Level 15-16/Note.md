# OverTheWire Bandit Write-Up: Level 15 → Level 16

## Objective
Retrieve the password for `bandit16` by submitting the current level's password (`bandit15`) to a service listening on `localhost` port `30001` using **SSL/TLS encryption**.

---

## How I Solved It

### 1. Checked the OpenSSL & `s_client` Manual Pages
Because port `30001` expects an encrypted SSL/TLS connection rather than plain text, I checked the `openssl` and `s_client` manual pages (`man openssl` and `man s_client`) to review the syntax for initiating an SSL/TLS client session from the terminal.

### 2. Retrieved the `bandit15` Password
Inside my `bandit15` session, I read the current level's password from `/etc/bandit_pass/bandit15` so I had it ready to send once the encrypted connection was established:

```bash
cat /etc/bandit_pass/bandit15
```
*Output:* `pbLYuZtTg4MgaqfJx8jbA9gKKGqM68A7`

### 3. Established an SSL/TLS Connection to `localhost:30001`
Using `openssl s_client` with the `-connect` flag, I connected to `localhost` on port `30001`:

```bash
openssl s_client -connect localhost:30001
```

After the TLS handshake completed (`TLSv1.3` with a self-signed certificate `CN=SnakeOil`) and the terminal reached `read R BLOCK`, I pasted the `bandit15` password:

```text
pbLYuZtTg4MgaqfJx8jbA9gKKGqM68A7
```

The server verified the password, returned `Correct!`, and printed the password for `bandit16` before closing the connection.

---

## Flag / Password Found
* **Bandit16 Password:** `k50Hf0u5HiXfWKNKiqXvPd0INGGa0XBV`

## What I Learned
* How to use **`openssl s_client -connect <host>:<port>`** to interact with network services secured by SSL/TLS encryption.
* How to read SSL/TLS handshake details in the terminal (certificate chain, TLS protocol version, and cipher suite) and recognize when the session is ready for input (`read R BLOCK`).
* The practical difference between unencrypted TCP connections (`nc`) and encrypted SSL/TLS connections (`openssl s_client`).

