# Steps for Development and Production

**_Note: Complete the Server Setup before proceeding with the steps below._**

## Development

- **Clone the repo**

```
git clone https://github.com/karthikeya0800/CRUD-Node.git
```

- **Build and Start the Docker Containers**

- Change environment variables in docker-compose.yml file to your own and run docker compose to start the development server.
- Remember to change API_UL = http://ip_address:PORT and rebuild the frontend before deploying to prod.

```
docker compose up -d
```

- A frontend folder will be generated in the current directory. This contains the production build of the frontend. **Do not delete this folder or change the name of the folder.**
- The react app will be running on `http://localhost:5173/`

Deployment Script

## Deployment Script

This script is used for deploying a frontend and backend application, as well as configuring Nginx and starting Docker Compose on a remote server. It utilizes SSH and SCP for file transfers and remote command execution.

### Pre-requisites

- Ensure you have SSH access to the remote server using an SSH key (`id_ed25519`).
- Nginx should already be installed and running on the remote server.
- Docker and Docker Compose should also be installed on the remote server.

---

### 1\. Copy Frontend Files to Temporary Directory on the Remote Server

    scp -i ~/.ssh/id_ed25519 -r ./frontend/* user@remote_ip:/tmp

This command copies all the frontend files from the local `./frontend` directory to the `/tmp` directory on the remote server. The `-i` option specifies the SSH key to use for authentication.

---

### 2\. Move Frontend Files to Nginx HTML Directory

    ssh -i ~/.ssh/id_ed25519 user@remote_ip "sudo rm -rf /var/www/html/* && sudo mv -f /tmp/* /var/www/html/"

On the remote server, this command removes any existing files in the `/var/www/html/` directory (the Nginx root directory) and moves the frontend files from `/tmp/` to `/var/www/html/`. The `sudo` command is used to perform these actions with administrative privileges.

---

### 3\. Copy Backend Files to Project Directory on the Remote Server

    scp -i ~/.ssh/id_ed25519 -r ./backend/* user@remote_ip:/path/to/project

This command copies the backend files from the local `./backend` directory to the specified project directory on the remote server.

---

### 4\. Copy Nginx Configuration to Temporary Directory

    scp -i ~/.ssh/id_ed25519 ./nginx.config user@remote_ip:/tmp

This command transfers the `nginx.config` file from the local machine to the `/tmp` directory on the remote server.

---

### 5\. Move Nginx Configuration to Nginx Directory

    ssh -i ~/.ssh/id_ed25519 user@remote_ip "sudo mv -f /tmp/nginx.config /etc/nginx/sites-available/default"

On the remote server, this command moves the `nginx.config` file from `/tmp/` to `/etc/nginx/sites-available/default`, replacing the existing configuration file. This requires `sudo` to have the necessary permissions.

---

### 6\. Remove Existing docker images,containers,volumes and networks

    ssh -i ~/.ssh/id_ed25519 user@remote_ip "sh docker-clear.sh"

This command navigates to the project directory on the remote server and runs custom script docker-clear.sh.

---

### 6\. Run Docker Compose

    ssh -i ~/.ssh/id_ed25519 user@remote_ip "cd /path/to/project && docker compose -f docker-compose.prod.yml up -d"

This command navigates to the project directory on the remote server and starts Docker Compose using the `docker-compose.prod.yml` file in detached mode (`-d`).

---

### 7\. Restart Nginx

    ssh -i ~/.ssh/id_ed25519 user@remote_ip "sudo systemctl restart nginx"

This command restarts the Nginx service on the remote server to apply the new configuration.

---

### 8\. Deployment Completed

The script prints a success message after completing all steps successfully.
