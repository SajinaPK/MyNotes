# Check for space issues

`strace -e statfs df /`   # disk space available 

`sudo du -sh * 2>/dev/null | sort -h`  # Find the largest directories under the parent dir

# Core Dumps
```
sudo sysctl -w kernel.core_pattern=core.%u.%p.%t
systemctl restart apport
ulimit -c unlimited
ulimit -a
```

# Delete all Docker stuff including volumes
```
sudo su
# service docker stop
# cd /var/lib/docker
# rm -rf *
# service docker start
```
# SSH Passwordless

To set up a passwordless SSH login in Linux all you need to do is to generate a public authentication key and append it to the remote hosts `~/.ssh/authorized_keys` file.

The following steps will describe the process for configuring passwordless SSH login:  

- *Check for existing SSH key pair.*

Before generating a new SSH key pair first check if you already have an SSH key on your client machine because you don’t want to overwrite your existing keys. Run the following ls command to see if existing SSH keys are present:   
`ls -al ~/.ssh/id_*.pub`  
If there are existing keys, you can either use those and skip the next step or backup up the old keys and generate a new one. If you see No such file or directory or no matches found it means that you do not have an SSH key and you can proceed with the next step and generate a new one. 

- *Generate a new SSH key pair.*

The following command will generate a new 4096 bits SSH key pair with your email address as a comment:  
`ssh-keygen -t rsa -b 4096 -C "your_email@domain.com"`  
To be sure that the SSH keys are generated you can list your new private and public keys with:  
`ls ~/.ssh/id_*`  
/home/yourusername/.ssh/id_rsa /home/yourusername/.ssh/id_rsa.pubCopy  

- *Copy the public key*

Now that you have generated an SSH key pair, in order to be able to login to your server without a password you need to copy the public key to the server you want to manage.  
The easiest way to copy your public key to your server is to use a command called ssh-copy-id. On your local machine terminal type:  
`ssh-copy-id remote_username@server_ip_address`  
You will be prompted to enter the remote_username password:  
`remote_username@server_ip_address's password:`  
Once the user is authenticated, the public key will be appended to the remote user authorized_keys file and connection will be closed.  

- *If by some reason the ssh-copy-id utility is not available on your local computer you can use the following command to copy the public key:*

`cat ~/.ssh/id_rsa.pub | ssh remote_username@server_ip_address "mkdir -p ~/.ssh && chmod 700 ~/.ssh && cat >> ~/.ssh/authorized_keys && chmod 600 ~/.ssh/authorized_keys"`

- *Login to your server using SSH keys*

After completing the steps above you should be able log in to the remote server without being prompted for a password.   
To test it just try to login to your server via SSH:  
`ssh remote_username@server_ip_address`  

If everything went well, you will be logged in immediately.

## Disabling SSH Password Authentication
To add an extra layer of security to your server you can disable the password authentication for SSH.
Before disabling the SSH password authentication make sure you can log in to your server without a password and the user you are logging in with has sudo privileges.

- Log into your remote server with SSH keys, either as a user with sudo privileges or root:  
`ssh sudo_user@server_ip_address`   

- Open the SSH configuration file /etc/ssh/sshd_config, search for the following directives and modify as it follows:  
`/etc/ssh/sshd_config`  
PasswordAuthentication no  
ChallengeResponseAuthentication no  
UsePAM no  

- Once you are done save the file and restart the SSH service.  

On Ubuntu or Debian servers, run the following command:  
`sudo systemctl restart ssh`  

On CentOS or Fedora servers, run the following command:  
`sudo systemctl restart sshd`  


# Create Sudo Users

Steps to Create a Sudo User
Follow the steps below to create a new user account and give it sudo access. If you want to configure sudo for an existing user, skip to step 3.

- 1 - Log in to your server.
Log in to your system as the root user:  
`ssh root@server_ip_address`  

- 2 - Create a new user account.
Create a new user account using the adduser command. Don’t forget to replace username with the user name that you want to create:  
`adduser username`  
You will be prompted to set and confirm the new user password. Make sure that the password for the new account is as strong as possible.

- 3 - Add the new user to the sudo group
By default on Ubuntu systems, members of the group sudo are granted with sudo access. To add the user you created to the sudo group use the usermod command:  
`usermod -aG sudo username`  
Test the sudo access  
Switch to the newly created user:  
`su - username`  
Use sudo to run the whoami command:  
sudo whoami  
If the user have sudo access then the output of the whoami command will be “root”:  
Output:  
root  

How to use sudo  
To use sudo, simply prefix the command with sudo and space:  
`sudo ls -l /root`  

The first time you use sudo in a session, you will be prompted to enter the user password:  
[sudo] password for username:  

# Crash on Linux produces no core or truncated core

**1. SET ULIMITS**

The ulimits for core (-c) and fsize (-f) need to be tuned so that the hard and soft limits are set to unlimited. This may require root access to change.
 
Global settings are set in the file `/etc/security/limits.conf`.  
The format for setting each limit is as follows:  
`<domain> <type> <item> <value>`  
`<domain>` controls which users or groups will have these limits  
`<type>` is either the string "soft" or "hard" limits.  
The hyphen "-" can also be used which represents both soft and hard limits  

Example: wasadmin    soft   core  unlimited  

**2. DISK SPACE**

To check all of your partitions, execute this command (the -k is for kilobytes):

`df -k`  

**3. Core pattern configuration**

To change the Linux core pattern, modify the /proc/sys/kernel/core_pattern file with root user privileges.

1. See what the core_pattern currently is:  
    `cat /proc/sys/kernel/core_pattern`

2. Save off the current core_pattern:  
    `cat /proc/sys/kernel/core_pattern > ~/old_core_pattern`

3. Set the core_pattern to core so it will write to the current working directory of the JVM.  
    `echo "core" > /proc/sys/kernel/core_pattern`

    or 

    To write the core file to a specific location use:  
    `echo "/path_to_directory/core" > /proc/sys/kernel/core_pattern`

4. Verify the core_pattern has been updated:  
    `cat /proc/sys/kernel/core_pattern`

**4. DISABLE SIGNAL HANDLERS**

To force the operating system to handle all signals sent to the JVM process, you can disable all JVM signal handlers.  
`-Xrs`  

**5. Other**

You can simulate a crash by sending a **signal 6** or **signal 11** to the JVM process. **This will terminate the process**.  
`kill -6 PID`  
  or  
`kill -11 PID`

An alternative is to use the **gcore** command. This produces a core file and **keeps the process running**.
`gcore PID`  

**How can I pipe a core to a specified program to control the core generation?**` 
To pipe to a specified program to control the core generation, use the following line. Ensure that program is configured to store core dumps and not truncate them.  
`|/path_to_directory/program_name`

This can be changed by having the Linux OS administrator edit /etc/sysctl.conf with the changes, save it, then run the command:  
`sysctl -p`  

** With either the ulimit or core_pattern changes, if there are problems, please contact your Linux OS Support. These are operating system settings, so your Linux OS Support will be able to assist you further.

Here are some examples of common configuration of core dump processing programs if you cannot change the core pattern string.
 
If /proc/sys/kernel/core_pattern is set to **|.../systemd-coredump**, then edit /etc/systemd/coredump.conf and update the defaults. For example, start with something like the following but change based on your available disk space. Change these values to your desired values. Disabled Compression helps reducing core dump production times.  
ProcessSizeMax=100G  
ExternalSizeMax=100G  
MaxUse=500G  
Compress=no  
 
Then run:  
`systemctl daemon-reload`
 
The JVM does not need to be restarted. Test creating a core dump. By default, it will be written to /var/lib/systemd/coredump/.

If /proc/sys/kernel/core_pattern is set to **|.../abrt-hook-ccpp,** then modify /etc/abrt/abrt.conf.
 Set MaxCrashReportsSize=0 to avoid truncation, and then run: systemctl restart abrtd
 
By default, cores will go to /var/spool/abrt.


If /proc/sys/kernel/core_pattern is set to **|...dynatrace...**, then the core dump should be further sent to the underlying core dump processing program. Review what that is by checking /opt/dynatrace/oneagent/agent/conf/original_core_pattern and then apply all the same logic as above.
