# OverTheWire Bandit Write-Up: Level 20 → Level 21

## Objective
Retrieve the password for `bandit21` by setting up a local Netcat listener on a chosen port, connecting to it using the setuid binary `./suconnect`, and returning the current `bandit20` password to receive the next level's password.

---

## How I Solved It

### 1. Opened a Split-Pane Terminal Session as `bandit20`
Because `./suconnect` connects to a local port and expects the `bandit20` password to be sent back over that connection, I opened two simultaneous `bandit20` SSH sessions side-by-side (using split panes) so one terminal could listen on a port while the other executed `./suconnect`.

### 2. Started a Netcat Listener on Port `9999` (Right Pane)
In the right terminal pane, I started a verbose Netcat listener on port `9999`:

```bash
nc -lvp 9999
```
*Output:* `Listening on 0.0.0.0 9999`

### 3. Connected with `./suconnect` and Sent the `bandit20` Password (Left & Right Panes)
In the left terminal pane, I ran `./suconnect` targeting port `9999`:

```bash
./suconnect 9999
```

Once the right pane confirmed `Connection received on localhost`, I pasted the `bandit20` password (`4pTjcunZ0fK2vmpJTwfG8Vf7VhxDeg0A`) into the Netcat listener in the right pane and pressed **Enter**.

* **Left Pane Output:**
  ```text
  Read: 4pTjcunZ0fK2vmpJTwfG8Vf7VhxDeg0A
  Password matches, sending next password
  ```
* **Right Pane Output:**
  The `./suconnect` binary validated the password and sent back the `bandit21` password directly over the open socket:
  ```text
  bM9kBv5WC3P4yoDyf12LSdGuNz5ka6hY
  ```

---

## Flag / Password Found
* **Bandit21 Password:** `bM9kBv5WC3P4yoDyf12LSdGuNz5ka6hY`

## What I Learned
* How to set up a local TCP listener using **`nc -lvp <port>`** (`-l` listen, `-v` verbose, `-p` local port) to receive and respond to incoming connections.
* How to coordinate two parallel terminal sessions (or background jobs) when testing client-server binaries like `./suconnect`.

