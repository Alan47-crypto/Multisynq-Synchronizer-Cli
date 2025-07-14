# Run Your Own Multisynq Synchronizer Node: A Beginner's Guide

This guide provides step-by-step instructions for installing, configuring, and running a Multisynq Synchronizer node. By running a node, you contribute to the network and earn points. This guide is designed for beginners, with no prior experience assumed.

---

## ✅ Step 1: Install Prerequisites (Ubuntu/Debian)

These commands will prepare your Ubuntu/Debian system with the necessary software.

### Install Node.js

First, we'll install Node.js (v18 or higher) and the `npm` package manager.

```bash
# Download and run the Node.js setup script
curl -fsSL https://deb.nodesource.com/setup_lts.x | sudo -E bash -

# Install Node.js
sudo apt-get install -y nodejs
```

### Install Docker

Next, install Docker Engine, which will run the synchronizer node.

```bash
# Download and run the official Docker installation script
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh

# Add your user to the docker group to run Docker commands without sudo
sudo usermod -aG docker ${USER}
```

**Important:** After running the last command, you must **log out and log back in** for the changes to take effect.

---

## 🔑 Step 2: Get Your Synq Key

To earn rewards, your node needs a unique Synq Key. Follow this process to get one.

1.  **Connect Your Wallet:** Go to the Multisynq sign-up page using this referral link: **[https://startsynqing.com/?ref=d31b0d-5p0wph](https://startsynqing.com/?ref=d31b0d-5p0wph)** and connect your Phantom wallet.

2.  **Get a Community ID:** Join the official **[Multisynq Discord](https://discord.gg/multisynq)**. Once there, follow their instructions to request a Community ID from the team.

3.  **Generate Your Synq Key:** Use the Community ID you received to generate your unique Synq Key.

Keep this Synq Key and your wallet address ready for the next step.

---

## 🚀 Step 3: Installation and Setup

Now we will install the command-line tool and configure it to run your node as a persistent service.

### 1. Install the CLI Tool

Open your terminal and run the following `npm` command to install `synchronizer-cli` globally on your system.

```bash
npm install -g synchronizer-cli
```

### 2. Configure Your Node

This command will launch an interactive setup guide. It will ask you for your **Synq Key** and **Wallet Address**.

```bash
synchronize init
```

Follow the prompts and paste your information when asked.

### 3. Run the Node as a Service

To ensure your node runs 24/7 and restarts automatically, we will set it up as a `systemd` service.

First, generate the service file:

```bash
synchronize service
```

Next, copy, enable, and start the service by running these four commands **one by one**:

```bash
sudo cp ~/.synchronizer-cli/synchronizer-cli.service /etc/systemd/system/
sudo systemctl daemon-reload
sudo systemctl enable synchronizer-cli
sudo systemctl start synchronizer-cli
```

### 4. Verify the Service is Running

Check the status of your newly created service.

```bash
synchronize status
```

If everything is correct, you should see a green `✅ Service status: active (running)` message, followed by recent logs showing a successful connection.

---

## 📊 Step 4: Monitoring Your Node with the Web Dashboard

The web dashboard gives you a real-time view of your node's performance.

### 1. Launch the Dashboard Server

In your terminal on the server, run:

```bash
synchronize web
```

This command will start the web server. It will remain running in your terminal to serve the dashboard page.

### 2. Access in Your Browser

Open your web browser and navigate to the appropriate address:

  * **If running on your local machine:** `http://localhost:3000`
  * **If running on a VPS or remote server:** `http://<your-server-ip>:3000`  
    *(Replace `<your-server-ip>` with your server's public IP address)*

### 💡 Firewall Tip!

If the web page does not load on your VPS, your server's firewall is likely blocking port `3000`. If you use `ufw` (common on Ubuntu), you can allow traffic with this command:

```bash
sudo ufw allow 3000/tcp
```

On the dashboard, you should see **Service Status: running** and the **Cumulative Traffic** value should increase over time. This is proof that your node is working!

---

## 💰 Step 5: Checking Your Points

To quickly check your lifetime earned points from the command line, run:

```bash
synchronize points
```

---

## 🔧 Troubleshooting Guide

If you run into issues, here are some common problems and their solutions.

### Issue: Service is not running (`❌ Service status: Not found or error`)

This is a general error. The best way to find the specific cause is to check the detailed logs.

  * **Solution:** Run the `journalctl` command to see the full service logs. This will almost always contain the specific error message.
    ```bash
    sudo journalctl -u synchronizer-cli --no-pager -n 50
    ```

### Issue: Logs show a "Container Name Conflict" error

The log shows `Error ... Conflict. The container name "/synchronizer-cli" is already in use`.

  * **Cause:** This happens when you have a manually started node (`synchronize start`) running at the same time as the system service. Docker does not allow two containers to have the same name.
  * **Solution:** Stop and remove the conflicting container, then restart your service.
    ```bash
    # Stop and remove the old container
    sudo docker stop synchronizer-cli && sudo docker rm synchronizer-cli

    # Restart the service
    sudo systemctl restart synchronizer-cli
    ```

### Issue: Cannot access the Web Dashboard in my browser

The page times out or refuses to connect.

  * **Cause:** This is almost always a firewall issue. Your server is blocking incoming traffic on port `3000`.
  * **Solution:** Allow port `3000` through your server's firewall. For `ufw` on Ubuntu:
    ```bash
    sudo ufw allow 3000/tcp
    ```
    Then, check your firewall status to ensure the rule was added:
    ```bash
    sudo ufw status
    ```
