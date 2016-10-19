---
layout: post
title: My adventures with Keys !!
---

# My Adventures with Keys !!

## Learn you some KEYs

- The private key is kept on the computer you log in from
- The public key is stored on the .ssh/authorized_keys file on
  - all the computers you want to log in to.
- What happens when you log in to a computer via SSH Key ?
  - the SSH server uses the public key to "lock" messages in a way that can only be "unlocked" by your private key 
- As an extra security measure, most SSH programs store the private key in a passphrase-protected format, 
  - so that if your computer is stolen or broken in to, you have enough time to disable your old public key
- Public key authentication is a much better solution than passwords for most people. 
- In fact, if you don't mind leaving a private key unprotected on your hard disk, 
  - you can even use keys to do secure automatic log-ins - as part of a network backup, for example. 
- Different SSH programs generate public keys in different ways, but they all generate public keys in a similar format:
  - <ssh-rsa or ssh-dss> <really long string of nonsense> <username>@<host>
- SSH can use either "RSA" (Rivest-Shamir-Adleman) or "DSA" ("Digital Signature Algorithm") keys. 
  - RSA is the only recommended choice for new keys, so this guide uses "RSA key" and "SSH key" interchangeably.


## Key based SSH login

### How - To create your public and private SSH keys:

```bash
mkdir ~/.ssh
chmod 700 ~/.ssh
ssh-keygen -t rsa

# Aliter: ssh-keygen -t rsa -b 4096
# The default is a 2048 bit key. You can increase this to 4096 bits with the -b flag 
```

- You will be prompted for a location to save the keys, and a passphrase for the keys. 
- This passphrase will protect your private key while it's stored on the hard drive:

```bash
Generating public/private rsa key pair.
Enter file in which to save the key (/home/b/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/b/.ssh/id_rsa.
Your public key has been saved in /home/b/.ssh/id_rsa.pub.
```

### How - To Transfer Client Key to Host

- The key you need to transfer to the host is the public one. 
- If you can log in to a computer over SSH using a password, run the following from your own computer:

```bash
ssh-copy-id <username>@<host>
```

- Aliter: ssh-copy-id "<username>@<host> -p <port_nr>"
- If you are using the standard port 22, you can ignore this tip.
- Aliter : Copy the public key file to the server and concatenate it onto the authorized_keys file manually. 

- It is wise to back that up first:

```bash

cp authorized_keys authorized_keys_Backup
cat id_rsa.pub >> authorized_keys
```

### Verify - Key based SSH login

```bash
ssh <username>@<host>

# You should be prompted for the passphrase for your key:

Enter passphrase for key '/home/<user>/.ssh/id_rsa':
```

- Note : that the host should be configured to allow key based logins

### Trouble - Still it asks for pw

- On the host computer, ensure that the /etc/ssh/sshd_config contains the following lines, 
  - and that they are **uncommented**

```bash
PubkeyAuthentication yes
RSAAuthentication yes
```

- Restart OpenSSH, and try logging in again. 
- If you get the passphrase prompt now, then congratulations, you're logging in with a key!

### Trouble - Permission Denied (Public Key)

- Chances are, your /home/<user> or ~/.ssh/authorized_keys permissions are too open by OpenSSH standards. 
- You can get rid of this problem by issuing the following commands:

```bash
chmod go-w ~/
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

### Trouble - Agent admitted failure to sign using the key

- This error occurs when the ssh-agent on the client is not yet managing the key.
- Issue the following commands to fix: 

```bash
ssh-add
```

### Trouble - Still more troubles - Wish I could debug ?

- start the sshd in debug mode

```bash
sudo /usr/sbin/sshd -d

ssh -v ( or -vv) username@host
```

### How - To disable password authentication, 

- look for the following line in your sshd_config file:

PasswordAuthentication yes
# replace it with a line that looks like this:
PasswordAuthentication no

- Once you have saved the file and restarted your SSH server, 

### Trouble - I cannot get out of troubles

- Google it

### Credits

> **Real credit goes to these articles. I have just rephrased !!**

- https://help.ubuntu.com/community/SSH/OpenSSH/Keys
- https://help.ubuntu.com/community/SSH/OpenSSH/Configuring#disable-password-authentication
