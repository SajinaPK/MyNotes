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
