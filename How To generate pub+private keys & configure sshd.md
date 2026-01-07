# key generation

A **public key** is stored on the **server** while a **private key** is stored on the **client** that will connect remotely.

The keys directory is `~/.ssh`

```
cd ~/Desktop
mkdir keys
cd keys
ssh-keygen -t rsa -C "paolo.bertani@kalei.it"
```

*Enter file in which to save the key:* leave empty to add the key into `~/.ssh/id_rsa` or type `id_rsa` to create a file with a single key in the current directory.

*Passphrase:* **empty**





# sshd_conf

### to edit the conf. file

    mate /etc/sshd_config

### disallow access with password

**Important**: keys must be installed before doing this to allow remote access!

    RSAAuthentication yes
    PubkeyAuthentication yes
    ChallengeResponseAuthentication no
    PasswordAuthentication no
    UsePAM no

### to relaunch sshd (with new settings)
    sudo launchctl stop com.openssh.sshd
