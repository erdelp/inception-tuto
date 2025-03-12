# 42 Inception: DigitalOcean Setup with GitHub Student Pack

## Goals of working on Inception like this

1. No sgoinfre slowlyness, no heavy VM that takes hours to boot with a GUI, you will be able to work on your VM from anywhere with a terminal (and a valid pubkey)
2. You will never ever in your developer life deploy a project with a VM with a GUI, always on a server over SSH
3. This will refresh the rudimentary server protection you learned on Born2BeRoot
4. You will actually deploy your inception project online with a domain name and all that for FREE !

The goal of this tutorial is not to tell you how to do your docker setup, just how to get an actual server and a more comfortable workspace for the project.

## Notes

1. This setup will work on your own VM too, just skip the DigitalOcean step, you will still have a VM without GUI (faster reboot, faster install)
2. I'm in no way affiliated with DigitalOcean it's just the free thing we get with the pack, you can use any other VPS provider (but you will pay)
3. If something is wrong in this tutorial DM me or make a pull request on the readme ! 


## Getting Free $200 Credit on DigitalOcean
If you're a student, you can get **$200 in free credit** for DigitalOcean through the GitHub Student Developer Pack:

This will allow you to get a VPS (Virtual Private Server) which is basically your own little VM hosted on DigitalOcean server, you will access it through SSH.

1. Go to [GitHub Student Developer Pack](https://education.github.com/pack).
2. Sign in with your **GitHub student account**.
3. Apply for the **DigitalOcean offer**.
4. Create an account using the provided link and link your github account **or you won't get the free credit**
5. It will ask for a credit card to check you're real but should not debit it (it will take like 1$ for 7 day to check it's working).
6. **Read carefully all the step above and check in the Billing section on DigitalOcean that you did get your credit**
7. **Remember to destroy your VPS once you're done with the project.**
8. **If you do not YOU WILL BE CREDITED ONCE THE 200$ CREDIT RUN OUT !!!!!**

Note: you need to go to [42 Github Portal](https://github-portal.42.fr.) first to activate it.

**Note: I'm not responsible if you get credited for the use of DigitalOcean, read step by step and check multiple time you're not being billed before starting your VPS (Droplet).
If you carefully follow the step you should not get debited.**

## Creating a DigitalOcean Droplet and Adding SSH Key

### 1. Create a Droplet
1. Log in to your **DigitalOcean account**.
2. Click on **Create → Droplets**.
3. Choose a **Debian image**.
4. Select your **Droplet size** (for inception 2vcpu and 2gb of RAM shoud be enough).
5. Choose a **datacenter region** (pick the closest one for better performance).
6. Under **Authentication**, select **SSH keys**.

### 2. Add Your SSH Key
1. On your local machine, check if you already have an SSH key:
   ```sh
   cat ~/.ssh/id_ed25519.pub || cat ~/.ssh/id_rsa.pub
   ```
   If you don’t have one, generate it using:
   ```sh
   ssh-keygen -t ed_25519
   ```
2. Copy your public key:
   ```sh
   cat ~/.ssh/ed_25519.pub
   ```
3. In DigitalOcean, click **New SSH Key**, paste your public key, and save it.
4. Click **Create Droplet** and wait for it to be ready.

### 3. Connect to Your Droplet
Once your droplet is ready, use the following command to connect:
```sh
ssh root@your_server_ip
```

## Securing Your Server

Contrary to Born2BeRoot a VPS is online and accessible to all, to avoid malicious attempts to access your VM here is a very basic secure setup.
You could push this further for future VPS you will run, look into Crowdsec and Fail2Ban but it's not neccessary for this project.

### 1. Create a New User
Create a new user named `login42`:
```sh
adduser login42
```
Add the user to the sudo group:
```sh
usermod -aG sudo login42
```

### 2. Change SSH Port to 4242
Edit the SSH configuration file:
```sh
nano /etc/ssh/sshd_config
```
Find the line:
```sh
#Port 22
```
Replace it with:
```sh
Port 4242
```

### 3. Disable Root Login and Password Authentication
Edit the SSH configuration file again:
```sh
nano /etc/ssh/sshd_config
```
Find and modify these lines to those values:
```sh
PermitRootLogin no
PasswordAuthentication no
PubkeyAuthentication yes
PermitEmptyPasswords no
AllowUsers login42
MaxAuthTries 3
LoginGraceTime 30
```
Save and exit (`CTRL+X`, then `Y`, then `Enter`).

### 4. Allow SSH Traffic on Port 4242 and Install UFW

```sh
sudo apt install ufw
sudo ufw allow 4242/tcp
sudo ufw enable
```

### 5. Restart SSH Service
Run the following command to apply changes:
```sh
systemctl restart ssh
```

### 6. Connect Using the New SSH Port
Now, you must specify the port when connecting:
```sh
ssh -p 4242 login42@your_server_ip
```

If it doesn't work you did something wrong, be sure to not logout from root until everything is well set.

If you can't login to your VPS anymore DigitalOcean has a recovery console you can use, check their documentation.

## Enabling X11 Forwarding (GUI Applications Over SSH)

The X Window System (X11, or simply X) is a windowing system for bitmap displays, common on Unix-like operating systems. Basically it manages windows and GUI Applications. 

To enable X11 forwarding , so here a browser to get access locally to login.42.fr follow these steps:

### 1. Enable X11 Forwarding on the Server
Edit the SSH configuration file again:
```sh
nano /etc/ssh/sshd_config
```
Find and modify these lines:
```sh
X11Forwarding yes
X11DisplayOffset 10
X11UseLocalhost no
TCPKeepAlive yes
ClientAliveInterval 60
ClientAliveCountMax 3
```
Save and restart SSH:
```sh
systemctl restart ssh
```

### 2. Connect with X Forwarding
From your local machine, connect using:
```sh
ssh -p 4242 -X root@your_server_ip
```

### 3. Set X Authority to enable X11Forwarding
After logging in, run:
```sh
nano ~/.bashrc
```
And add this line at the top
```sh
export XAUTHORITY=~/.Xauthority
```
Reconnect one last time in ssh with the -X flag, you can run GUI applications remotely!

Let's test it 
```sh
sudo apt install chromium
chromium
```

You should have a browser window open that is running locally on your VM.

---

## WARNING

### Remember to destroy your VPS once you're done with the project. 

### If you do not YOU WILL BE CREDITED ONCE THE 200$ CREDIT RUN OUT !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!

---

## Further setup, access your server online with a domain name !!

Remember to install comfort application like VIM, Chromium, oh-my-bash, it will make all this easier.

Again, the goal of this tutorial is not to tell you how to do your docker setup, just how to get an actual server and a more comfortable workspace for the project.

### 1. Once you have your docker setup working

Edit your nginx.conf file to get access to your server online ! (As I said I won't tell you how to)

Then allow port 443 on UFW
```sh
sudo allow 443/tcp
sudo ufw reload
```

### 2. Setup a domain name for your server

1. Go to [DuckDNS.org](https://duckdns.org).
2. Add a domain, something like **http://yourlogin-inception42.duckdns.org** would fit.
3. Once the domain is created link it to your server ip below and click update ip.
4. Wait a couple minute to a couple hour
5. You should be able to access your wordpress and other settings from this address !!
6. Your SSL key being self signed you will have an error message you can bypass
7. If you want an actual SSL key for your DuckDNS domain look into Let's Encrypt and Certbot.
8. Or use CloudFlare as a proxy to your domain (this won't work with DuckDNS though you will need your own TDL).

### 3. Security notes

1. If you did do this note that the wordpress and nginx install you did are not very secure.
2. Therefore you should always put your server down (docker compose down) and not leave it running if you're not attending the project at this time.

### 4. Getting the .env file locally for your Makefile

1. You should not push your .env file or docker secret to github or the vogosphere.
2. Therefore you can just store it locally (for example in /var/)
3. Then just add this line at the beginning of your makefile:
```sh
cp /var/.env ./srcs/
```
4. This will copy your local .env file where it needs to be at each runtime, making the evaluation comfier.


Congrats you deployed a functioning server for the first time ! 
Enjoy your **42 Inception** ! 🚀

## WARNING

### Remember to destroy your VPS once you're done with the project. 

### If you do not YOU WILL BE CREDITED ONCE THE 200$ CREDIT RUN OUT !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
