## Week 5 Homework Submission File: Archiving and Logging Data

Please edit this file by adding the solution commands on the line below the prompt.

Save and submit the completed file for your homework submission.

---

### Step 1: Create, Extract, Compress, and Manage tar Backup Archives

1. Command to **extract** the `TarDocs.tar` archive to the current directory:
---
- mkdir ~/Projects
- cd ~/Projects   ----> Once in the folder, download TarDocs.tar as directed in Gitlab.
- tar -xvvf TarDocs.tar
---
2. Command to **create** the `Javaless_Doc.tar` archive from the `TarDocs/` directory, while excluding the `TarDocs/Documents/Java` directory:
---
- tar czf Javaless_Docs.tar.gz --exclude "TarDocs/Documents/Java" TarDocs/Documents
---

3. Command to ensure `Java/` is not in the new `Javaless_Docs.tar` archive:
---
- tar tvvf Javaless_Docs.tar.gz | grep Java
---

**Bonus** 
- Command to create an incremental archive called `logs_backup_tar.gz` with only changed files to `snapshot.file` for the `/var/log` directory:

#### Critical Analysis Question

- Why wouldn't you use the options `-x` and `-c` at the same time with `tar`?
-c is used to create tar
-x is used to untar .tar files.
---

### Step 2: Create, Manage, and Automate Cron Jobs

1. Cron job for backing up the `/var/log/auth.log` file:
---
- 0 6 * * 3 sudo tar -cvzpf /var/log/logs_backups.tar.tgz /var/log/auth.log
---

### Step 3: Write Basic Bash Scripts

1. Brace expansion command to create the four subdirectories:
---
- mkdir -p ~/backups/{freemem,diskuse,openlist,freedisk}
---

2. Paste your `system.sh` script edits below:

    ```bash
#!/bin/bash
free -h > ~/backups/freemem/free_mem.txt
du -h > ~/backups/diskuse/disk_use.txt
lsof > ~/backups/openlist/open_list.txt
df -h > ~/backups/freedisk/free_disk.txt
    ```

3. Command to make the `system.sh` script executable:
chmod +x system.sh

**Optional**
- Commands to test the script and confirm its execution:
---
- cd ~  ----> Select the right directory where script is stored/saved.
- sudo ./system.sh
---
**Bonus**
- Command to copy `system` to system-wide cron directory:
---
sudo cp system.sh /etc/cron.weekly/
---

### Step 4. Manage Log File Sizes
 
1. Run `sudo nano /etc/logrotate.conf` to edit the `logrotate` configuration file. 

    Configure a log rotation scheme that backs up authentication messages to the `/var/log/auth.log`.
    ---
    - cd /etc/logrotate.d/  ----> Correct system folder for conf files
    - nano auth   ----> write custom conf
    ---

    - Add your config file edits below:

    ```bash
    /var/log/auth.log {
        rotate 7 
        weekly
        missingok
        notifempty
        delaycompress
        compress

}
    ```
---

### Bonus: Check for Policy and File Violations

1. Command to verify `auditd` is active:
---
- systemctl status auditd
---

2. Command to set number of retained logs and maximum log file size:

    - Add the edits made to the configuration file below:

    ```bash
    #
# This file controls the configuration of the audit daemon
#
local_events = yes
write_logs = yes
log_file = /var/log/audit/audit.log
log_group = adm
log_format = RAW
flush = INCREMENTAL_ASYNC
freq = 50
max_log_file = 35
num_logs = 7
priority_boost = 4
disp_qos = lossy
dispatcher = /sbin/audispd
name_format = NONE
#
    ```

3. Command using `auditd` to set rules for `/etc/shadow`, `/etc/passwd` and `/var/log/auth.log`:


    - Add the edits made to the `rules` file below:

    ```bash
## First rule - delete all
-D

## Increase the buffers to survive stress events.
## Make this bigger for busy systems
-b 8192

## This determine how long to wait in burst of events
--backlog_wait_time 0

## Set failure mode to syslog
-f 1

### Monitor /etc/shadow
auditctl -w /etc/shadow -p wra -k hashpass_audit

## Monitor /etc/passwd
auditctl -w /etc/passwd -p wra -k userpass_audit

## Monitor /var/log/auth.log
auditctl -w /var/log/auth.log -p wra -k authlog_audit

    ```

4. Command to restart `auditd`:
---
systemctl restart auditd
---

5. Command to list all `auditd` rules:
---
- sudo auditctl -l
---

6. Command to produce an audit report:
---
sudo aureport -k 
---

7. Create a user with `sudo useradd attacker` and produce an audit report that lists account modifications:

8. Command to use `auditd` to watch `/var/log/cron`:
---
sudo auditctl -w /var/log/cron -k cronlog_audit
---

9. Command to verify `auditd` rules:

---

### Bonus (Research Activity): Perform Various Log Filtering Techniques

1. Command to return `journalctl` messages with priorities from emergency to error:

1. Command to check the disk usage of the system journal unit since the most recent boot:

1. Comand to remove all archived journal files except the most recent two:

1. Command to filter all log messages with priority levels between zero and two, and save output to `/home/sysadmin/Priority_High.txt`:

1. Command to automate the last command in a daily cronjob. Add the edits made to the crontab file below:

    ```bash
    [Your solution cron edits here]
    ```

---
© 2020 Trilogy Education Services, a 2U, Inc. brand. All Rights Reserved.
