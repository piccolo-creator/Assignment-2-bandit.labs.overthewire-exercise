# OverTheWire Bandit Write-Up: Level 14 → Level 15

## Objective
Retrieve the password for `bandit15` by submitting the current level's password (`bandit14`) to a network service listening on `localhost` port `30000`.

---

## How I Solved It

### 1. Checked the Manual for Netcat (`nc`)
Before connecting to the local port, I reviewed the `nc` (Netcat) manual page (`man nc`) to understand its basic syntax (`nc [options] hostname port`) and how it establishes TCP connections to read and write data across network ports.

### 2. Retrieved the `bandit14` Password
Inside the `bandit14` session, I read the current level's password from `/etc/bandit_pass/bandit14` so I could submit it to the local service:

```bash
cat /etc/bandit_pass/bandit14
```
*Output:* `aaWecNkG4FhxJQxz07uiwzVP6bJiYS65`

### 3. Connected to `localhost` on Port `30000`
Using Netcat (`nc`), I opened a TCP connection to `localhost` on port `30000` and pasted the `bandit14` password:

```bash
nc localhost 30000
aaWecNkG4FhxJQxz07uiwzVP6bJiYS65
```

The server validated the password, responded with `Correct!`, and returned the password for `bandit15`.

---

## Flag / Password Found
* **Bandit15 Password:** `pbLYuZtTg4MgaqfJx8jbA9gKKGqM68A7`

## What I Learned
* How to use **Netcat (`nc <host> <port>`)** to open raw TCP connections and interact directly with local network services.
* How to inspect command usage and syntax with `man nc`.
* How local services on `localhost` (`127.0.0.1`) listen on specific ports to accept and validate user input.

