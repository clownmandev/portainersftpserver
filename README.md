SFTPortress: A Docker SFTP Backup Server for Portainer
This project, "SFTPortress," provides a simple, secure, and persistent SFTP server using Docker. It's designed to be a "drop-in" backup target, where all uploaded files are safely stored on the host machine.

These instructions are tailored for deployment using the Portainer web interface.

Features
Persistent Storage: All uploaded files are saved to a local folder on the Docker host.

Secure and Isolated: The SFTP service runs in an isolated container. The user is jailed to their home directory and cannot access the rest of the system.

Easy Deployment via Portainer: Get up and running in minutes using the Portainer UI, with no command-line usage required.

Secure Credential Management: Use Portainer's environment variable manager to keep passwords safe and out of your configuration files.

Deployment with Portainer (Recommended)
Step 1: Create a New Stack
In your Portainer dashboard, navigate to Stacks from the left-hand menu.

Click the Add stack button.

Step 2: Configure the Stack
Name: Give your stack a name, for example, sftportress.

Web editor: Make sure you are in the web editor tab. Paste the entire content of the docker-compose.yml file into the editor.

# docker-compose.yml content to paste:
version: '3.8'
services:
  sftp-server:
    image: atmoz/sftp:latest
    container_name: sftportress-server
    volumes:
      - ./sftp-data:/home/${SFTP_USER:-backupuser}/uploads
    ports:
      - "2222:22"
    command: ${SFTP_USER:-backupuser}:${SFTP_PASS:-changeme}:1001
    restart: unless-stopped

Step 3: Set Your Credentials (Environment Variables)
This is the most important step for setting your secure password.

Scroll down below the web editor to the Environment variables section.

Click the Add environment variable button twice to create two new variables:

Variable 1:

Name: SFTP_USER

Value: backupuser (or any username you prefer)

Variable 2:

Name: SFTP_PASS

Value: Enter a strong, unique password here.

Ensure the "Advanced mode" toggle is on if you don't see the option to add variables.

Step 4: Deploy the Stack
Scroll to the bottom of the page and click the Deploy the stack button.

Portainer will now pull the image and start your SFTP server. You can view its status in the Containers menu.

How to Connect and Upload Files
Use any standard SFTP client (like FileZilla or WinSCP) with the following details:

Host/Address: The IP address of your Portainer/Docker host machine.

Port: 2222

Username: The SFTP_USER you set in the environment variables.

Password: The SFTP_PASS you set.

Accessing Your Backed-Up Files
A new folder named sftp-data will be created on your Docker host in the directory where Portainer stores its stack configurations (e.g., /var/lib/docker/volumes/sftportress/_data). Any files you upload will appear in the uploads sub-folder within that directory.

Logging and Management in Portainer
To view live logs: Go to Containers, click on the sftportress-server container, and then click the Logs icon.

To stop/start/restart the server: Use the controls at the top of the container's details page.

(Alternative) Deployment via Command Line
For users not using Portainer, you can deploy using the command line.

Create a docker-compose.yml file with the content from Step 2.

Create a separate .env file in the same directory:

SFTP_USER=backupuser
SFTP_PASS=YourSecurePasswordHere

Run docker-compose up -d to start the server.
