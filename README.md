# PP4

## Goal

In this exercise you will:

* Use SSH to connect to remote servers from WSL, macOS, or Linux shells, understanding the handshake and authentication process.
* Generate an Ed25519 SSH key pair and explain the concept of digital signatures.
* Configure your local SSH client via the `~/.ssh/config` file for streamlined access.
* Securely copy files between local and remote hosts using `scp`, including local-to-remote, remote-to-local, and remote-to-remote transfers.
* Automate startup tasks on the remote server by writing a shell script that runs at login and explaining the role of `~/.bashrc` vs. `~/.profile`.

**Important:** Start a stopwatch when you begin and work uninterruptedly for **90 minutes**. Once time is up, stop immediately and record exactly where you paused.

---

## Workflow

1. **Fork** this repository
2. **Modify & commit** your solution
3. **Submit your link for Review**

---

## Prerequisites

* Several starter repos are available here:
  [https://github.com/orgs/STEMgraph/repositories?q=SSH%3A](https://github.com/orgs/STEMgraph/repositories?q=SSH%3A)
* Consult the SSH and SCP man-pages for detailed options and explanations:

  * `man ssh`
  * `man scp`

---

## Tasks

### Task 1: SSH Login

**Objective:** Establish an SSH connection and observe each stage of the process.

1. From your local shell (WSL, macOS Terminal, or Linux), log into the `vorlesungsserver` (or any other remote machine of your choice, e.g. your own raspberry pi):

   ```bash
   ssh youruser@remotehost
   ```
2. Carefully observe and note each step:

   * **TCP connection** to port 22 on `remotehost`.
   * **SSH protocol handshake**: key exchange and algorithm negotiation.
   * **Authentication**: public-key or password exchange.
   * **Shell allocation**: your remote session starts.
3. After login, exit the session with `exit`.

Provide:


 1) The exact ssh command you ran

   ```bash
   ssh oknaguesob@128.140.85.215
   ```

 2) A detailed, step-by-step explanation of what happened at each stage
<summary>
TCP Connection : Your SSH client tries to open a TCP connection to port 22 on the IP 128.140.85.215.
SSH Protocol Handshake : Your machine and the server exchange keys and negotiate encryption algorithms.
Authentication : You'll be prompted for a password 
Shell Allocation : If authentication succeeds, your shell session starts.
Exit : This will close the SSH session and return you to your local shell.
</summary>


---

### Task 2: Ed25519 Key Pair

**Objective:** Create a secure key pair and explain how digital signatures verify identity.

1. Generate an Ed25519 SSH key pair:

   ```bash
   ssh-keygen -t ed25519 -C "your_email@example.com"
   ```

   * Accept the default file location (`~/.ssh/id_ed25519`). Or provide the `-f <filepath>` option additionally.
   * Enter a passphrase when prompted (optional).
2. Locate and inspect your `id_ed25519` (private key) and `id_ed25519.pub` (public key).
3. Install your key on the remote machine (e.g. `vorlesungsserver`.
4. Explain in writing:

   * How the **private key** is used to sign challenges.
   * How the **public key** on the server verifies signatures without revealing the private key.
   * Why Ed25519 is preferred (performance, security).

Provide:

 1) The ssh-keygen command you ran
```bash
ssh-keygen -t ed25519 -C "oceane@example.com"
```
 2) The file paths of the generated keys
```bash
/home/okndaguesob/.ssh/id_ed25519
```

 3) Your written explanation (3–5 sentences) of the signature process
<summary>
when I connect the server sends a random challenge and the SSH client signs it using my private key.If validbyou are authenticated otherweise the server denies access.</summary>
<summary>
Because the server already has my public key stored in ~/.ssh/authorized_keys it will verify the digital signature sent by the client.If the signature is valid it confirms my identity that mean the SSH session is allowed.</summary>
<summary>
Ed25519 is prefer because it is high secure has a fast performance has small size modern Design</summary>



---

### Task 3: SSH Config File

**Objective:** Simplify SSH commands via `~/.ssh/config`.

1. Open (or create) `~/.ssh/config` in `vim`.
2. Add entries for your hosts, for example:

   ```text
   Host my-remote
       HostName remote.example.com
       User youruser
       IdentityFile ~/.ssh/id_ed25519

   Host backup-server
       HostName backup.example.com
       User backupuser
       Port 2222
       IdentityFile ~/.ssh/id_ed25519_backup
   ```
3. Save and close the file, then test:

   ```bash
   ssh my-remote
   ssh backup-server
   ```
4. Explain:

   * How SSH reads `~/.ssh/config` and matches hosts.
   * The difference between `HostName` and `Host`.
   * How aliases prevent long commands.

Provide:


 1) The full contents of your ~/.ssh/config
```test
  Host my-remote
    HostName 128.140.85.215
    User oceane
    IdentityFile ~/.ssh/id_ed25519

Host backup-server
    HostName 128.140.85.215
    User okndaguesob
    Port 2222
    IdentityFile ~/.ssh/id_ed25519_backup
```
 2) A short explanation (3–4 sentences) of how the config simplifies connections
<summary>
Host: This is the nickname or your definted alias you. And the HostName: This is the real or IP address  of the remote server you're connecting to.</summary>
<summary>
SSH looks up Host my-remote, sees HostName remote.example.com, and connects to that.</summary>
<summary>
Aliases in your ~/.ssh/config prevent long SSH commands by allowing you to assign a short name to a full SSH configuration. This lets you connect with a simple, short command instead of typing out a long one each time.</summary>
```

---

### Task 4: SCP File Transfers

**Objective:** Practice copying files securely using `scp`.

1. **Local → Remote**:

   ```bash
   scp /path/to/localfile.txt youruser@remotehost:~/destination/
   ```
2. **Remote → Local**:

   ```bash
   scp youruser@remotehost:~/remotefile.log ./local_destination/
   ```
3. **Remote → Remote** (between two directories on the same remote host):

   ```bash
   scp -r youruser@remotehost:/path/dir1 youruser@remotehost:/path/dir2
   ```
4. For each command:

   * Verify file timestamps and sizes after transfer, using `ls -la`
   * Note any flags you used (e.g., `-r`, `-P` for port).
5. Explain:

   * How `scp` initiates an SSH session for each transfer.
   * The role of encryption in protecting data in transit.

**Provide:**


 1) Each scp command you ran
**Local → Remote
 ```bash
 scp /home/okndaguesob/Documents/example.txt okndaguesob@128.140.85.215:~/destination/
 ```
**Remote → Local:
 ```bash
scp okndaguesob@128.140.85.215:~/remotefile.log ./local_destination/
 ```
**Remote → Remote
 ```bash
scp -r okndaguesob@128.140.85.215 :/home/okndaguesob/dir1 okndaguesob@128.140.85.215 :/home/okndaguesob/dir2
 ```
 2) Any flags or options used
<summary>
-r allow the copy of entire directories</summary>
<summary>
-P use the port when copying files</summary>
<summary>
-la shows detailed list of all file</summary>
 3) A brief explanation (2–3 sentences) of scp’s mechanism
<summary>
SSH  uses strong encryption algorithms to secure data as it travels across the network.</summary>
<summary>
This encryption protects : The contents of your files ; your username, password, and any other sensitive information ; the integrity of the data (prevents tampering).</summary>
```

---

### Task 5: Login Shell Script & Profile Explanation

**Objective:** Automate commands at login and understand shell initialization files.

1. On the **remote** server, create a script `~/login_tasks.sh` containing at least three commands you find useful (e.g., `echo "Welcome $(whoami)"`, `uptime`, `ls ~/projects`). You may either use `vim` or try the following to create a file from your commandline directely:

   ```bash
   cat << 'EOF' > ~/login_tasks.sh
   #!/usr/bin/env bash
   echo "Welcome $(whoami)! Today is $(date)."
   uptime
   ls ~/projects
   EOF
   chmod +x ~/login_tasks.sh
   ```

> The files content should be something akin to:
> ```bash
> #!/usr/bin/env bash
> echo "Welcome $(whoami)! Today is $(date)."
> uptime
> ls ~/projects
> ```

2. Append to your `~/.bashrc` (or `~/.profile` if using a login shell) a line to source this script on each new session:

   ```bash
   echo "source ~/login_tasks.sh" >> ~/.bashrc
   ```
3. Log out and log back in to trigger the script.
4. Explain:

   * The difference between `~/.bashrc` and `~/.profile` (interactive vs. login shells).
   * Why and when each file is read.
   * How sourcing differs from executing.

**Provide:**


 1) The contents of login_tasks.sh
```bash
cat << 'EOF' > ~/login_tasks.sh
>echo "Welcome $(whoami)! Today is $(date)."
>uptime
>ls ~/projects
>EOF
chmod +x ~/login_tasks.sh
```
 2) The lines you added to ~/.bashrc or ~/.profile
```bash
echo "source ~/login_tasks.sh" >> ~/.bashrc
```

 3) Your explanation (3–5 sentences) of shell init files and sourcing vs. executing
</summary>
~/.bashrc is read when you open an interactive non-login shell, like when you open a terminal in your desktop session.
Purpose: Sets up your shell environment for normal, interactive use — things like aliases, prompts, or functions</summary>
</summary>
~/.profile (or sometimes ~/.bash_profile) is read by login shells, such as when you SSH into a remote machine.</summary>
Purpose: Initializes environment variables and startup programs
</summary>
Sourcing  runs the script in the current shell, so any variables or changes it makes persist.</summary>
</summary>
Executing runs the script in a new subprocess, so any environment changes won’t affect the current shell.</summary>
```

---

**Remember:** Stop working after **90 minutes** and record where you stopped.
