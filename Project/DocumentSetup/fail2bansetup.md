**fail2ban setup**

TL:DR

Installed fail2ban on our AlmaLinux server via EPEL with dnf, after adding a 2 GB swap file because the 891 MB RAM made dnf crash.

Enabled the sshd jail with nftables bans in jail.local, since firewalld is off, and verified it’s running and catching failed logins.

Run this command to check what fail2ban has banned

```bash
$ sudo fail2ban-client status sshd
```

-------------------------------------------------------------------------------------------------
Heres how i worked through setting up fail2ban on our server

These commands does not work since we are using alma Linux for the server

```bash
$ sudo apt install -y fail2ban
$ sudo systemctl enable fail2ban
$ sudo systemctl start fail2ban
```

Therefore we need to setup fail2ban differently

We check to see if our firewall is active or not by running this command

```bash
$ systemctl is-active firewalld
```
We then get this answer "inactive"

We could enable our firewall, but we already have a layer of protection from azure, and 
enabling it cus throw off the ports our server is running on if we dont exclude everything we're currently using.

So we have to download EPEL for alma Linux (extra packages) because fail2ban isn't in alma Linux's default downloads

so we run this command

```bash
$ sudo dnf install -y epel-release
```

Although this command does not work, since our server only has 1GB, so the server kills the download

So we have some options, we can either upgrade our azure from 1GB to 2GB or more, which costs more money.
Stop the server temporaily to save space while downloading.
Or add some memory from our disk file which has 61GB available rn

I'll attempt to burrow 2GB from the disk, since it costs no money and wont affect the server or our users.

I've called in claude cus I dont wanna fuck anything up lol

Alright so basically we had to take some of the disk space and put it over as useable memory for dnf to install things. 

I ran these commands one at a time

```bash
$ sudo fallocate -l 2G /swapfile
$ sudo chmod 600 /swapfile
$ sudo mkswap /swapfile
$ sudo swapon /swapfile
```

Afterwords I echo'ed this line and piped it into the fstab file, so the memory we got (the 2gb) stays when we restart the server.

```bash
$ echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
```


After that I ran "sudo systemctl daemon-reload" which was stated inside the file to do, so it updates.

Now I can try and install EPEL again with dnf via this command

```bash
$ sudo dnf install -y epel-release
```

And it worked and installed properly this time!
Now we can try and install fail2ban again

Since we are running on Alma Linux and not ubuntu fail2ban needs to have some kind of firewall, to know who to ban, so we make sure we have nftables installed on our server, which is already have.

We use nftables because it does NOT "block everything" by default like firewalld would if we enabled it.

Now we install fail2ban

```bash
$ sudo dnf install -y fail2ban
```

It worked and was listed as "Complete!"

Since we run Alma Linux, fail2ban still doesn't do anything, so we have to set it up. I used nano for this inside of "/etc/fail2ban/jail.local"

where i wrote this 
```
[DEFAULT]
banaction = nftables-multiport

[sshd]
enabled = true
```

Sets fail2ban to work like it does by default in Ubuntu like Anders showed us in the guide.

Now we just have to enabled it using these commands Anders gave us

```bash
$ sudo systemctl enable fail2ban
$ sudo systemctl start fail2ban
```

Enable makes it so it starts everytime on reboots etc
Start makes it start right now.

Both ran fine, and now lets check if its working using this command

systemctl is-enabled fail2ban prints "enabled" which is good

We also run these commands to check that everything is working like we want it to, and using nftables correctly and using our setup in jail.local

$ sudo fail2ban-client status -> Checks how many its put in jail

sudo fail2ban-client get sshd actions -> Check to see if we're using nftables like we want or firewalld

And thats fail2ban finally set up:)

Run this command to check every so often if theres any banned IPs

```bash
$ sudo fail2ban-client status sshd
```
