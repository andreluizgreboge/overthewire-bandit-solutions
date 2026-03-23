# Bandit OverTheWire: Implementation Memorial

This repository documents the technical rationale and solutions for the **Bandit** wargame from [OverTheWire](https://overthewire.org/wargames/bandit/). These exercises focus on Linux command-line proficiency, networking, and information security.

**Environment:** Native Linux Terminal  
**Target:** `bandit.labs.overthewire.org`  
**Port:** `2220`

---

## Level 0: Establishing Connectivity

* **Goal:** Access the remote server via SSH.
* **Rationale:** SSH (Secure Shell) is the standard protocol for encrypted remote administration. Since the service operates on a non-standard port (2220), the `-p` flag is mandatory to override the default (22).
* **Action:**
  ```bash
  ssh -p 2220 bandit0@bandit.labs.overthewire.org
  ```
* **Credentials:** `bandit0` / `bandit0`

---

## Level 0 → 1: Basic File Navigation

* **Goal:** Retrieve the password stored in a file named `readme` in the home directory.
* **Rationale:** Standard directory listing and file output are the primary methods for data retrieval in a CLI environment.
* **Action:**
  1. **List directory contents:**
     ```bash
     bandit0@bandit:~$ ls
     readme
     ```
  2. **Output file content:**
     ```bash
     bandit0@bandit:~$ cat readme
     ```
* **Password:** `ZjLjTmM6FvvyRnrb2rfNWOZOTa6ip5If`

---

## Level 1 → 2: Handling Special Characters

* **Goal:** Retrieve the password from a file named `-`.
* **Rationale:** In Linux, many commands interpret a standalone hyphen (`-`) as an instruction to read from `stdin` (Standard Input). To treat it as a filename, you must specify its path.
* **Action:** 
  Used the `./` prefix to distinguish the file from a command option.
  ```bash
  bandit1@bandit:~$ cat ./-
  ```
* **Password:** `263JGJPfgU6LtdEvgfWU1XP5yac29mFx`

---

## Level 2 → 3: Files with Spaces

* **Goal:** Read a file named `spaces in this filename`.
* **Rationale:** The shell uses spaces to separate arguments. To read a filename containing spaces, the string must be escaped or enclosed in quotes to be treated as a single entity.
* **Action:**
  Listed contents with `ls -la` to identify the exact filename, then used quotes to read it.
  ```bash
  bandit2@bandit:~$ cat "./spaces in this filename"
  ```
* **Password:** `MNk8KNH3Usiio41PRUEoDFPqfxLPlSmx`

---

## Level 3 → 4: Hidden Files in Directories

* **Goal:** Find a hidden file within the `inhere` directory.
* **Rationale:** Files starting with a dot (`.`) are considered hidden in Linux and do not appear with a simple `ls`. The `-a` (all) flag is required to reveal them.
* **Action:**
  1. Entered the directory: `cd inhere`
  2. Revealed the hidden file: `ls -la`
  3. Read the discovered file:
     ```bash
     bandit3@bandit:~/inhere$ cat ...Hiding-From-You
     ```
* **Password:** `2WmrDFRmJIq3IPxneAaMGhap0pFhF3NJ`

---

## Level 4 → 5: Identifying Human-Readable Data

* **Goal:** Find the only human-readable file in a directory full of binary data.
* **Rationale:** When multiple files have similar names (starting with `-`), you can use wildcards and tools like `grep` or `file` to distinguish plain text from binary "garbage."
* **Action:**
  Used `grep` to search through all files in the directory. The binary files return a "binary file matches" notification, while the text file displays its content.
  ```bash
  bandit4@bandit:~/inhere$ grep "" ./*
  ```
* **Password:** `4oQYVPkxZOOEOO5pTW81FB8j8lxXGUQw`

---

## Level 5 → 6: Advanced File Searching (Properties)

* **Goal:** Locate a file with specific attributes: human-readable, 1033 bytes in size, and not executable.
* **Rationale:** The `find` command is essential for filtering by size (`-size`), type (`-type f`), and other metadata across multiple subdirectories.
* **Action:**
  Executed a search for a file exactly 1033 bytes (`1033c`) in size.
  ```bash
  bandit5@bandit:~/inhere$ find . -type f -size 1033c
  ./maybehere07/.file2
  ```
* **Password:** `HWasnPhtq9AVKe0dmk45nxy20cvUa6EG`

---

## Level 6 → 7: System-Wide Search with Permissions

* **Goal:** Find a file anywhere on the server owned by user `bandit7`, group `bandit6`, and exactly 33 bytes in size.
* **Rationale:** Searching the entire root (`/`) directory usually triggers many "Permission denied" errors. Redirecting `stderr` (2) to `/dev/null` cleans the output.
* **Action:**
  Used `find` from the root directory with ownership filters and error redirection.
  ```bash
  bandit6@bandit:~$ find / -user bandit7 -group bandit6 -size 33c 2>/dev/null
  /var/lib/dpkg/info/bandit7.password
  ```
* **Password:** `morbNTDkSW6jIlUc0ymOdMaLnOlFVAaj`

---

Seguindo o padrão, aqui está o bloco dos níveis 7 ao 12 pronto para o seu README.md. Usei a técnica de proteção para garantir que o conteúdo chegue intacto.

Markdown
---

## Level 7 → 8: Searching Specific Strings

* **Goal:** Find the password in `data.txt` next to the word "millionth".
* **Rationale:** When dealing with massive text files, manual searching is inefficient. `grep` allows for immediate pattern matching.
* **Action:**
  ```bash
  bandit7@bandit:~$ grep "millionth" data.txt
  ```
* **Password:** `dfwvzFQi4mU0wfNbFOe9RoWskMLg7eEc`

---

## Level 8 → 9: Unique Lines in Sorted Data

* **Goal:** Find the only line of text that occurs exactly once in `data.txt`.
* **Rationale:** The `uniq` command only detects adjacent duplicate lines. Therefore, the file must be sorted first for `uniq -u` to properly filter out all non-unique entries.
* **Action:**
  ```bash
  bandit8@bandit:~$ sort data.txt | uniq -u
  ```
* **Password:** `4CKMh1JI91bUIZZPXDqGanal4xvAg0JM`

---

## Level 9 → 10: Extracting Strings from Binary

* **Goal:** Retrieve the password from a binary file, preceded by several `=` characters.
* **Rationale:** Binary files are unreadable via `cat`. The `strings` utility extracts sequences of printable characters, which can then be piped into `grep`.
* **Action:**
  ```bash
  bandit9@bandit:~$ strings data.txt | grep "=="
  ```
* **Password:** `FGUW5ilLVJrxX9kMYMmlN4MgbpfMiqey`

---

## Level 10 → 11: Base64 Decoding

* **Goal:** Decode a Base64-encoded string stored in `data.txt`.
* **Rationale:** Base64 is a common encoding scheme for representing binary data in ASCII string format. Decoding it returns the original plaintext.
* **Action:**
  ```bash
  bandit10@bandit:~$ echo "VGhlIHBhc3N3b3JkIGlzIGR0UjE3M2ZaS2IwUlJzREZTR3NnMlJXbnBOVmozcVJyCg==" | base64 -d
  ```
* **Password:** `dtR173fZKb0RRsDFSGsg2RWnpNVj3qRr`

---

## Level 11 → 12: ROT13 Substitution Cipher

* **Goal:** Decrypt a password obfuscated with the ROT13 cipher.
* **Rationale:** ROT13 is a simple substitution cipher that replaces a letter with the 13th letter after it in the alphabet. It is an involution (applying it twice restores the original).
* **Action:**
  Used the `tr` (translate) command to map the alphabet 13 places forward.
  ```bash
  bandit11@bandit:~$ echo "Gur cnffjbeq vf 7k16JArUVv5LxVuJfsSVdbbtaHGlw9D4" | tr "a-zA-Z" "n-za-mN-ZA-M"
  ```
* **Password:** `7x16WNeHIi5YkIhWsfFIqoognUTyj9Q4`

---

## Level 12 → 13: Decompression Nightmare

* **Goal:** Extract the password from a file (`data.txt`) that has been repeatedly compressed using different formats (Hexdump, Gzip, Bzip2, Tar).
* **Rationale:** File extensions in Linux are often unreliable or missing. The `file` command is the only way to verify the actual encoding/compression of a binary blob.
* **Action:** 
  1. Converted hexdump back to binary: `xxd -r data.txt > data.bin`
  2. Repeatedly analyzed and decompressed:
     * **Gzip:** `mv file file.gz && gzip -d file.gz`
     * **Bzip2:** `bzip2 -d file`
     * **Tar:** `tar -xf file`
  3. Final extraction of the ASCII password after reaching the last layer.
* **Password:** `FO5dwFsc0cbaIiH0h8J2eUks2vdTDwAn`

---

## Level 13 → 14: Private Key Authentication

* **Goal:** Use the private key found in `bandit13` to log into `bandit14`.
* **Rationale:** SSH can use RSA key pairs instead of passwords. However, the private key file must have restricted permissions (`600`) or SSH will reject it for being "too open."
* **Action:**
  1. **Transfer:** Downloaded the key:
     ```bash
     scp -P 2220 bandit13@bandit.labs.overthewire.org:~/sshkey.private ./bandit14.key
     ```
  2. **Set Permissions:** `chmod 600 bandit14.key`
  3. **Login:**
     ```bash
     ssh -i bandit14.key bandit14@bandit.labs.overthewire.org -p 2220
     ```
* **Password:** `MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS` (Key based login)

---

## Level 14 → 15: Localhost Service Interaction

* **Goal:** Submit the current password to a service on `localhost` port 30000.
* **Rationale:** This tests basic network interaction. `nc` (Netcat) allows sending raw data to a specific port.
* **Action:**
  ```bash
  bandit14@bandit:~$ nc localhost 30000
  ```
* **Password:** `8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo`

---

## Level 15 → 16: SSL/TLS Encrypted Communication

* **Goal:** Submit the password to a service on port 30001 that requires encryption.
* **Rationale:** Standard Netcat doesn't handle SSL handshakes. `openssl s_client` is used to establish an encrypted tunnel.
* **Action:**
  ```bash
  bandit15@bandit:~$ openssl s_client -connect localhost:30001
  ```
* **Password:** `kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx`

---

## Level 16 → 17: Port Scanning & Service Discovery

* **Goal:** Find which port (31000-32000) is an SSL service that returns a new key.
* **Rationale:** Using `nmap` with service versioning (`-sV`) filters out simple echo services from the actual credential-returning service.
* **Action:**
  1. **Scan:** `nmap -sV -p 31000-32000 localhost`
  2. **Retrieve:** Connected to the identified port via `openssl s_client`.
  3. **Setup:** Saved the resulting RSA key as `bandit17.key` locally and set `chmod 600`.
* **Password:** (RSA Private Key)

---

## Level 17 → 18: File Comparison (diff)

* **Goal:** Identify the password by finding the difference between two files.
* **Rationale:** `diff` is the standard tool for comparing data sets and identifying changes.
* **Action:**
  ```bash
  bandit17@bandit:~$ diff passwords.old passwords.new
  ```
* **Password:** `x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO`

---

## Level 18 → 19: Bypassing Interactive Shells

* **Goal:** Access a file on a login that immediately terminates the session.
* **Rationale:** SSH can execute a command directly without starting an interactive TTY session, bypassing `.bashrc` or `.profile` scripts that force a logout.
* **Action:**
  ```bash
  ssh bandit18@bandit.labs.overthewire.org -p 2220 "cat readme"
  ```
* **Password:** `cGWpMaKXVwDUNgPAVJbWYuGHVn9zl3j8`

---

## Level 19 → 20: SUID Binary Exploitation

* **Goal:** Read a file owned by `bandit20` using a SUID executable.
* **Rationale:** SUID bits allow a program to run with the file owner's privileges. This allowed me to "impersonate" bandit20 to read its password file.
* **Action:**
  ```bash
  bandit19@bandit:~$ ./bandit20-do cat /etc/bandit_pass/bandit20
  ```
* **Password:** `0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO`

---

## Level 20 → 21: Network Listener Synchronization

* **Goal:** Use a SUID binary that validates the password by connecting to a local port.
* **Rationale:** The binary `suconnect` expects a listener to provide the current password. Netcat is used in the background (`&`) to act as that server.
* **Action:**
  1. **Listen:** `echo "0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO" | nc -l -p 1234 &`
  2. **Connect:** `./suconnect 1234`
* **Password:** `EeoULMCra2q0dSkYj561DX7s1CpBuOBt`

---


## Level 21 → 22: Analysis of Scheduled Tasks (Cron)

* **Goal:** Retrieve the password by analyzing a script executed by a Cronjob.
* **Rationale:** Cron is a time-based job scheduler in Unix-like systems. By inspecting the system-wide cron definitions in `/etc/cron.d/`, we can find scripts running with higher privileges.
* **Action:**
  1. Inspected the cronjob: `cat /etc/cron.d/cronjob_bandit22`
  2. Analyzed the associated script: `cat /usr/bin/cronjob_bandit22.sh`
  3. The script redirects the password to a specific file in `/tmp/`.
  4. Read the target file to get the credential.
* **Password:** `tRae0UfB9v0UzbCdn9cY0gQnds9GF58Q`

---

## Level 22 → 23: Reverse Engineering Script Logic

* **Goal:** Predict the output filename of a cronjob script that uses dynamic naming.
* **Rationale:** The script uses an MD5 hash of a string containing the username to generate a "hidden" filename in `/tmp/`. By replicating the command logic for the next user (`bandit23`), we can find where their password is being stored.
* **Action:**
  1. Analyzed `/usr/bin/cronjob_bandit23.sh`.
  2. Replicated the hash generation: 
     ```bash
     echo "I am user bandit23" | md5sum | cut -d ' ' -f 1
     ```
  3. Accessed the resulting file in `/tmp/8ca319486bfbbc3663ea0fbe81326349`.
* **Password:** `0Zf11ioIjMVN551jX3CmStKLYqjk54Ga`

---

## Level 23 → 24: Script Injection via Spooler

* **Goal:** Create and execute a custom script using a cronjob that runs every minute.
* **Rationale:** The cronjob for `bandit24` executes every script placed in `/var/spool/bandit24/foo/` and then deletes it. By creating a script with global execution and write permissions, we can force the system to copy the password file to a directory we control.
* **Action:**
  1. Created a workspace in `/tmp/`.
  2. Wrote a bash script (`solucao.sh`) to copy the password:
     ```bash
     #!/bin/bash
     cat /etc/bandit_pass/bandit24 > /tmp/meu_script_ia/senha.txt
     ```
  3. Set full permissions (`chmod 777`) to the script, the output file, and the directory.
  4. Copied the script to the spooler: `cp solucao.sh /var/spool/bandit24/foo/`.
  5. Waited for the cron cycle and read `senha.txt`.
* **Password:** `gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8`

---

## Level 24 → 25: Network Brute Force (PIN Cracking)

* **Goal:** Crack a 4-digit PIN by sending the password and PIN combination to a local port.
* **Rationale:** The service on port 30002 validates a password followed by a PIN. Since there are only 10,000 possible combinations (0000-9999), a simple `for` loop in Bash can automate the brute force attack.
* **Action:**
  Used a one-liner to generate all combinations and pipe them into Netcat, filtering out the "Wrong" responses.
  ```bash
  for i in {0000..9999}; do 
    echo "gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8 $i"
  done | nc localhost 30002 | grep -v "Wrong"
  ```
* **Password:** `iCi86ttT4KSNe1armKiwbQNmB3YJP3q4`

---

---

## Level 25 → 26: Breaking Out of Restricted Shells

* **Goal:** Escape a restricted shell that uses `view` (a vim-based pager) as the default login shell.
* **Rationale:** When a user's shell is set to a text viewer, the session ends as soon as the viewer closes. However, by shrinking the terminal window, you force `view` into command mode (due to the small display area). From there, you can invoke a standard shell.
* **Action:**
  1. Shrink the terminal window to a very small size.
  2. Log in; the small window prevents the script from finishing, leaving you in `view`.
  3. Type `:set shell=/bin/bash` followed by `:shell`.
  4. Once in the shell, use the SUID binary to get the password.
* **Password:** `upsNCc7vzaRDx6oZC6GiR6ERwe1MowGB`

---

## Level 26 → 27: Introduction to Git Repositories

* **Goal:** Retrieve a password stored in a remote Git repository.
* **Rationale:** Version control systems like Git are often used to host code, but they can inadvertently store sensitive credentials in the base directory.
* **Action:**
  1. Created a temporary directory and cloned the repository:
     ```bash
     git clone ssh://bandit27-git@bandit.labs.overthewire.org:2220/home/bandit27-git/repo
     ```
  2. Accessed the `repo` folder and read the `README` file.
* **Password:** `Yz9IpL0sBcCeuG7m9uQFt8ZNpS4HZRcN`

---

## Level 27 → 28: Git History & Information Leaks

* **Goal:** Find a password that was removed or obscured in the current version of a Git repository.
* **Rationale:** Git tracks every change ever made. Even if a password is "deleted" in the latest commit, it remains in the repository's history (the `.git` folder).
* **Action:**
  1. Cloned the repository: `bandit28_repo`.
  2. Inspected the logs to find commits related to "info leaks" or "missing data":
     ```bash
     git log
     ```
  3. Used `git log -p` to view the actual diff (differences) between commits, revealing the deleted password line.
* **Password:** `4pT1t5DENaYuqnqvadYs1oE4QLCdjmJ7`

---

## Level 28 → 29: Git Branches & Hidden Data

* **Goal:** Find a password that is not present in the `master` branch of the Git repository.
* **Rationale:** Git allows for "branching," creating separate lines of development. A developer might "clean up" a password in the main branch but leave it exposed in a development or testing branch.
* **Action:**
  1. Cloned the repository: `bandit29_repo`.
  2. Checked for all available branches (including remote ones):
     ```bash
     git branch -a
     ```
  3. Switched to the suspicious branch (usually named `dev`, `experimental`, or similar):
     ```bash
     git checkout dev
     ```
  4. Read the `README.md` or the code in that specific branch to find the cleartext password.
* **Password:** `4pT1t5DENaYuqnqvadYs1oE4QLCdjmJ7`

---

## Level 29 → 30: Git Branch Discovery

* **Goal:** Find the password in a repository where the `master` branch appears to be a dead end.
* **Rationale:** Developers often use different branches for development. While the `master` branch might be "clean," a `dev` or `experimental` branch may still contain sensitive information.
* **Action:**
  1. Cloned the repository: `bandit29_repo`.
  2. Checked for all branches: `git branch -a`.
  3. Switched to the relevant branch (e.g., `git checkout dev`) to reveal the password in the `README.md`.
* **Password:** `qp30ex3VLz5MDG1n91YowTv4Q8l7CDZL`

---

## Level 30 → 31: Git Tags & Hidden References

* **Goal:** Retrieve the password from a repository where neither the files nor the log show any credentials.
* **Rationale:** Git Tags are used to mark specific points in a repository's history (usually for releases). Since tags can point to objects or commits that aren't on the current branch's main line, they are a common place to hide data.
* **Action:**
  1. Cloned the repository: `bandit30_repo`.
  2. Checked for tags:
     ```bash
     git tag
     ```
  3. Discovered a tag named `secret`.
  4. Inspected the tag content:
     ```bash
     git show secret
     ```
* **Password:** `fb5S2xb7bRyFmAvQYQGEqsbhVyJqhnDy`

---

---

## Level 31 → 32: Git Hooks & Interaction

* **Goal:** Submit a specific file to the remote repository to trigger a validation script.
* **Rationale:** Many Git servers use `pre-receive` or `post-receive` hooks. These are scripts that run on the server side when someone pushes code. In this level, the hook validates if a file named `key.txt` exists with the correct content.
* **Action:**
  1. Created the required file: `echo "May I come in?" > key.txt`
  2. Forced the addition (ignoring potential `.gitignore` rules): `git add -f key.txt`
  3. Committed and pushed to the master branch:
     ```bash
     git commit -m "Submit key"
     git push origin master
     ```
  4. The server-side hook intercepted the push and returned the password before rejecting the commit.
* **Password:** `3O9RfhqyAlVBEZpVb6LYStshZoqoSx5K`

---

## Level 32 → 33: Positional Parameters & Shell Escape

* **Goal:** Escape a restricted shell (`sh`) that converts all input to uppercase.
* **Rationale:** In shell scripting, `$0` is a positional parameter that refers to the name of the current shell or script being executed. In many restricted environments, calling `$0` invokes a new, unrestricted instance of the shell, bypassing the uppercase filter.
* **Action:**
  1. Executed `$0` in the restricted prompt.
  2. Once in the new shell, read the password file:
     ```bash
     cat /etc/bandit_pass/bandit33
     ```
* **Password:** `tQdtbs5D5i2vJwkO8mEyYEyTL8izoeJ0`

---

## Level 33: Game Completion

* **Status:** Final Level Reached.
* **Notes:** The `README.txt` in the home directory confirms the completion of the Bandit wargame.
* **Action:**
  ```bash
  bandit33@bandit:~$ cat README.txt
  ```
> "Congratulations on solving the last level of this game!"

---



