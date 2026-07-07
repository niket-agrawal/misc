# Setting up and Securing a New VPS Instance

This guide walks you through the essential steps to secure and configure a new VPS instance, including setting up a non-root user, configuring SSH keys for secure login, and connecting the VPS to a domain name.

Note: Order of these steps do not matter, except when it is logical.

---

## Step 1: Create and adding a Non-Root User with Sudo Access, (i.e. by adding to sudo group)

**There can be two ways to create a new user**, `adduser` or `useradd`.
      Use `adduser` when you want to add as admin, system prompts for additional user information
   But, use `useradd` when you want to add as users, with custom home directory and other group options
   ```bash
   root@vmd200007:~# adduser admin1   # Using adduser (recommended for interactive setup)
     New password: xxxxxxxx
   # root@vmd200007:~# useradd -m -s /bin/bash -G sudo admin1 # if you prefer to avoid prompts:

   root@vmd200007:~# usermod -aG sudo admin1
   root@vmd200007:~# groups admin1     # to see groups for this user
   ```
   
**Now, switch to the new user to configure SSH**, optional but recommended step. You can either log out or login again using password set earlier, or use `su` command. Before this step, generate SSH keys on your local machine (if you haven't already). On windows, go to `C:\Users\niket\.ssh` directory, and use `ssh-keygen -t ed25519 -C "email@example.com"`. It should give you both public and private keys on your PC.
   To generate keys, you can also use another algorithm,
     ```bash
   ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa -C "your_email@example.com"
   ```
   ```bash
   root@vmd200007:~# su - admin1
   admin1@vmd200007:~$ pwd
   admin1@vmd200007:~$ mkdir .ssh
   admin1@vmd200007:~$ nano .ssh/authorized_keys
     # Paste your LOCAL/WINDOWS machine's public SSH key (~/.ssh/id_rsa.pub)
     # Save: Ctrl+X → Y → Enter
   admin1@vmd200007:~$ chmod 600 .ssh/authorized_keys
   # verify key-based login
   $ ssh admin1@178.18.248.217 # Should connect without password prompt. If successful, you're secure.
   ```

---

## Step 2: Set Timezone, Install Essential Utilities

   Set the **timezone**
   ```bash
   admin1@vmd200007:~$ timedatectl                                # see current time
   admin1@vmd200007:~$ timedatectl list-timezones | grep Kolkata  # list available timezones, and select desired
   admin1@vmd200007:~$ sudo timedatectl set-timezone Asia/Kolkata # set the timezone  
   admin1@vmd200007:~$ timedatectl                                # see verify
   ```
   
   And then, **install essential utilities**
   ```bash
   admin1@vmd200007:~$ sudo apt install htop vim git zip unzip gnupg2 -qq
   # htop for harware monitoring
   # vim for editing
   # git for version control
   # zip unzip for common functions
   # gnupg2 for gpg keys verification and creation
   admin1@vmd200007:~$ htop --version         # verify installations
   admin1@vmd200007:~$ vim --version          # verify installations
   admin1@vmd200007:~$ git --version          # verify installations
   admin1@vmd200007:~$ git --version          # verify installations
   admin1@vmd200007:~$ git --version          # verify installations
   
   admin1@vmd200007:~$ sudo apt install ncdu
   admin1@vmd200007:~$ bash -c "$(curl -sLo- https://superfile.dev/install.sh)"
   # ncdu for file explorer
   # spf for file explorer
   ```

---

## Step 3: VPS Hardening, Network and Firewall policy

1. **UFW (Uncomplicated Firewall)** – Install & configure/ It is a wrapper for iptables. Use these default ports, --> 80-http, 443-https, 22-ssh. The rules are automatically added for both IPv4 and IPv6 (shown as `(v6)` in the status output).
```bash
admin1@vmd200007:~$ sudo apt install ufw
admin1@vmd200007:~$ sudo ufw status
admin1@vmd200007:~$ sudo ufw default deny incoming            # set a default “deny” incoming policy
admin1@vmd200007:~$ sudo ufw allow 22/tcp
admin1@vmd200007:~$ sudo ufw allow 80/tcp
admin1@vmd200007:~$ sudo ufw allow 443/tcp
admin1@vmd200007:~$ sudo ufw enable                           # enable the firewall
admin1@vmd200007:~$ sudo ufw status                           # verify the active rules
admin1@vmd200007:~$ sudo ufw delete allow xxxx                # to delete a port
admin1@vmd200007:~$ sudo ufw allow 2250/tcp                   # to allow a tcp port
admin1@vmd200007:~$ sudo ufw allow 2250                       # to allow a port
admin1@vmd200007:~$ sudo ufw deny xxxx                        # to delete a port
```


---









 
