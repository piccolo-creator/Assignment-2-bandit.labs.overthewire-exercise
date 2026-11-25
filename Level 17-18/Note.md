# OverTheWire Bandit Write-Up: Level 17 → Level 18

## Objective
Find the password for `bandit18` by comparing two files in the home directory (`passwords.old` and `passwords.new`) and identifying the only line that changed between them.

---

## How I Solved It

### 1. Listed the Files in the Home Directory
Once logged into `bandit17`, I checked the contents of the home directory using `ls`:

```bash
ls
```
*Output:* `passwords.new  passwords.old`

### 2. Compared `passwords.new` and `passwords.old` with `diff`
I used the `diff` utility to compare both files line by line and isolate the modified entry:

```bash
diff passwords.new passwords.old
```

*Output:*
```text
42c42
< OQxX7JF1ndr98ZuhOTDY8FonT8SZTTXT
---
> IWJWh8k7THApiWp99dOlsruErLQZrR9
```

### 3. Identified the New Password
In `diff` output (`42c42`), line 42 changed between the two files:
* **`<`** shows the line in the first file (`passwords.new`): `OQxX7JF1ndr98ZuhOTDY8FonT8SZTTXT`
* **`>`** shows the line in the second file (`passwords.old`): `IWJWh8k7THApiWp99dOlsruErLQZrR9`

Since `passwords.new` holds the updated password, `OQxX7JF1ndr98ZuhOTDY8FonT8SZTTXT` is the password for `bandit18`.

---

## Flag / Password Found
* **Bandit18 Password:** `OQxX7JF1ndr98ZuhOTDY8FonT8SZTTXT`

## What I Learned
* How to use **`diff <file1> <file2>`** to quickly spot differences between two large text files.
* How to read standard `diff` change indicators (`42c42` meaning line 42 changed, `<` for the first file, and `>` for the second file).

