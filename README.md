# 42 Project: DigitalOcean Setup with GitHub Student Pack

## Getting Free $200 Credit on DigitalOcean
If you're a student, you can get **$200 in free credit** for DigitalOcean through the GitHub Student Developer Pack:

1. Go to [GitHub Student Developer Pack](https://education.github.com/pack).
2. Sign in with your **GitHub student account**.
3. Apply for the **DigitalOcean offer**.
4. Once approved, create an account on [DigitalOcean](https://www.digitalocean.com/).
5. Redeem your **$200 credit** from the "Billing" section.

## Creating a DigitalOcean Droplet and Adding SSH Key

### 1. Create a Droplet
1. Log in to your **DigitalOcean account**.
2. Click on **Create → Droplets**.
3. Choose an **Ubuntu image**.
4. Select your **Droplet size** (a small instance is enough for most tasks).
5. Choose a **datacenter region** (pick the closest one for better performance).
6. Under **Authentication**, select **SSH keys**.

### 2. Add Your SSH Key
1. On your local machine, check if you already have an SSH key:
   ```sh
   cat ~/.ssh/id_rsa.pub
   ```
   If you don’t have one, generate it using:
   ```sh
   ssh-keygen -t rsa -b 4096
   ```
2. Copy your public key:
   ```sh
   cat ~/.ssh/id_rsa.pub
   ```
3. In DigitalOcean, click **New SSH Key**, paste your public key, and save it.
4. Click **Create Droplet** and wait for it to be ready.

### 3. Connect to Your Droplet
Once your droplet is ready, use the following command to connect:
```sh
ssh root@your_server_ip
```

## Setting Up SSH on Port 4242
By default, SSH runs on port `22`, but for security, **42 School requires using port 4242**. Follow these steps to set it up:

### 1. Change SSH Port to 4242
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
Save and exit (`CTRL+X`, then `Y`, then `Enter`).

### 2. Restart SSH Service
Run the following command to apply changes:
```sh
systemctl restart ssh
```

### 3. Allow SSH Traffic on Port 4242
If you're using a firewall (UFW), allow the new port:
```sh
ufw allow 4242/tcp
```

### 4. Connect Using the New SSH Port
Now, you must specify the port when connecting:
```sh
ssh -p 4242 root@your_server_ip
```

## Enabling X11 Forwarding (GUI Applications Over SSH)

To enable X11 forwarding (useful for running GUI applications), follow these steps:

### 1. Enable X11 Forwarding on the Server
Edit the SSH configuration file:
```sh
nano /etc/ssh/sshd_config
```
Find and modify these lines:
```sh
X11Forwarding yes
X11DisplayOffset 10
X11UseLocalhost no
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

### 3. Set X Authority
After logging in, run:
```sh
export XAUTHORITY=~/.Xauthority
```
Now, you can run GUI applications remotely!

---
Enjoy your **42 Project** setup on DigitalOcean! 🚀
