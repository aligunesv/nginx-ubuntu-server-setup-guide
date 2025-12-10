# 🌐 Nginx Web Server Setup & Configuration on Ubuntu

This project serves as a step-by-step guide to installing, configuring, and securing an Nginx web server on a Linux (Ubuntu) environment. It covers basic static file hosting, reverse proxy configurations, and SSL implementation.

## 📋 Prerequisites

  * A server running **Ubuntu** (Physical or Virtual Machine).
  * A user with `sudo` privileges.
  * Basic knowledge of the command line interface.
  * *For Step 5 (SSL):* A registered domain name pointing to your server's IP address.

-----

## 🚀 1. Installation

First, ensure your package lists are up to date to avoid installing outdated software.

```bash
sudo apt update
```

Install the Nginx web server:

```bash
sudo apt install nginx
```

Once installed, verify that the service is running:

```bash
sudo systemctl status nginx
```

> **Note:** You should see `active (running)` in the output.

-----

## 🛡️ 2. Firewall Configuration (UFW)

To allow external access to your web server, you must configure the firewall. Ubuntu uses **UFW** (Uncomplicated Firewall) by default.

Check the available application profiles:

```bash
sudo ufw app list
```

Allow Nginx traffic. We use 'Nginx Full' to open both Port 80 (HTTP) and Port 443 (HTTPS):

```bash
sudo ufw allow 'Nginx Full'
```

Verify the status:

```bash
sudo ufw status
```

You can now test your server by visiting your IP address in a browser:

  * `http://YOUR_SERVER_IP`
  * `http://localhost` (if testing locally)

-----

## 📂 3. Hosting Static Websites

By default, Nginx serves files located in `/var/www/html`. To host your own website, follow these steps:

1.  Navigate to the web root directory:

    ```bash
    cd /var/www/html/
    ```

2.  Copy your website files (HTML, CSS, JS) to this folder.
    *(Assuming your project is in a folder named `websitefolder` in your home directory)*:

    ```bash
    sudo cp -r ~/websitefolder/* .
    ```

3.  Verify the files are there:

    ```bash
    ls -l
    ```

Refresh your browser to see your custom website live\!

-----

## ⚙️ 4. Advanced Configuration & Port Forwarding

For professional setups, you should create custom server blocks instead of editing the default configuration.

### A. Create a New Configuration File

```bash
sudo nano /etc/nginx/sites-available/my_project
```

### B. Configuration Examples

#### Option 1: Static Site Hosting

Paste this if you just want to serve HTML files:

```nginx
server {
    listen 80;
    server_name your_domain.com OR your_ip_address;

    root /var/www/html;
    index index.html index.htm;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

#### Option 2: Reverse Proxy (Port Forwarding)

Paste this if you are running an app (like Node.js, Python, Go) on a different port (e.g., 3000) and want Nginx to forward traffic to it:

```nginx
server {
    listen 80;
    server_name your_domain.com OR your_ip_address;

    location / {
        # Forwards traffic to the app running on port 3000
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

### C. Enable the Configuration

1.  Create a symbolic link to the `sites-enabled` directory:

    ```bash
    sudo ln -s /etc/nginx/sites-available/my_project /etc/nginx/sites-enabled/
    ```

2.  Test the configuration for syntax errors:

    ```bash
    sudo nginx -t
    ```

3.  Restart Nginx to apply changes:

    ```bash
    sudo systemctl restart nginx
    ```

-----

## 🔒 5. Securing with SSL (HTTPS)

We will use **Certbot** and **Let's Encrypt** to obtain a free SSL certificate.
*⚠️ Requirement: You must own a domain name (e.g., example.com) pointing to your server IP for this to work.*

1.  **Install Certbot and the Nginx plugin:**

    ```bash
    sudo apt install certbot python3-certbot-nginx
    ```

2.  **Obtain the Certificate:**
    Run the following command and follow the interactive prompts (select "Redirect" when asked to force HTTPS):

    ```bash
    sudo certbot --nginx -d your_domain.com -d www.your_domain.com
    ```

3.  **Verify Auto-Renewal:**
    Let's Encrypt certificates last for 90 days. Certbot installs a timer to renew them automatically. Check it:

    ```bash
    sudo systemctl status certbot.timer
    ```

Your site is now secure\! Visit `https://your_domain.com` to see the padlock icon. 🔒

-----

## 📝 Cheat Sheet: Useful Commands

| Command | Description |
| :--- | :--- |
| `sudo systemctl start nginx` | Start the server |
| `sudo systemctl stop nginx` | Stop the server |
| `sudo systemctl restart nginx` | Restart (stops and starts) |
| `sudo systemctl reload nginx` | Reload config without dropping connections |
| `sudo nginx -t` | Test configuration syntax |

-----

## 🤝 Contributing

1.  Fork this repository.
2.  Create a new feature branch (`git checkout -b feature/AmazingFeature`).
3.  Commit your changes (`git commit -m 'Add some AmazingFeature'`).
4.  Push to the branch (`git push origin feature/AmazingFeature`).
5.  Open a Pull Request.
