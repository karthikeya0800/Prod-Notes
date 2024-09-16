# Setting Up Production Server (Linux)

## Directory Setup

- Create a new directory for the project

```
mkdir my-project
cd my-project
```

## Install Docker

### Download link [Docker](https://docs.docker.com/engine/install/ubuntu/)

1. Set up Docker's apt repository.

```
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```

2. Install the Docker packages.

```
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

3. Verify that the Docker Engine installation is successful by running the hello-world image.

```
sudo docker run hello-world
```

# Install Nginx

1. Update the package index and install the Nginx package.

```
sudo apt-get update
sudo apt-get install nginx
```

2. Start the Nginx service and enable it to start on boot.

```
sudo systemctl start nginx
sudo systemctl enable nginx
```

3. Verify that Nginx is running by accessing the server's IP address in a web browser.

```
http://your_server_ip OR
http://your_domain
```

<!-- 4. Give write permission to the user

```
sudo chown -R <USERNAME HERE> /var/www/html
sudo chmod -R u+w /var/www/html
``` -->

## Generate SSL Certificate

1. Create a new bash script to install certbot and nginx plugin and setup auto renew

```
#!/bin/bash

# Install Certbot and Nginx plugin
sudo apt update
sudo apt install certbot python3-certbot-nginx -y

# Replace with your domain name
DOMAIN="your-domain.com"
EMAIL="your-email@example.com"  # Email for renewal notifications (not mandatory but recommended)

# Obtain SSL certificate from Let's Encrypt
sudo certbot --nginx -d $DOMAIN -d www.$DOMAIN --email $EMAIL --agree-tos --no-eff-email --redirect

# Test Certbot auto-renewal to ensure it's working
sudo certbot renew --dry-run

# Set up a cron job for automatic renewal
echo "0 0,12 * * * root certbot renew --quiet" | sudo tee /etc/cron.d/certbot-renew

# Restart Nginx to apply changes
sudo systemctl restart nginx

echo "SSL certificate installation and auto-renew setup complete!"

```
