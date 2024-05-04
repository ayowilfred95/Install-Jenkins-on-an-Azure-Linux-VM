```markdown
# Running Jenkins on Azure VM

This README provides instructions for setting up Docker on an Azure VM where Jenkins is running. Docker is required for building and managing Docker containers, which may be part of your Jenkins pipeline.

## Installing Docker

1. **SSH into your Azure VM**: Use SSH to access your Azure VM where Jenkins is hosted.

2. **Install Docker**: If Docker is not already installed on the VM, you can install it by following the official Docker installation guide for Ubuntu. Here's a simplified version of the commands you might use:

   ```bash
   sudo apt update
   sudo apt install -y apt-transport-https ca-certificates curl software-properties-common
   curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
   sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
   sudo apt update
   sudo apt install -y docker-ce
   ```

3. **Add Jenkins user to Docker group (optional)**: To allow the Jenkins user to run Docker commands without sudo, you can add the Jenkins user to the Docker group:

   ```bash
   sudo usermod -aG docker jenkins
   ```

   Replace `jenkins` with the username of the Jenkins user if it's different.

4. **Restart Jenkins**: After installing Docker, restart the Jenkins service to ensure it recognizes the Docker installation:

   ```bash
   sudo systemctl restart jenkins
   ```

5. **Verify Docker installation**: After the restart, verify that Docker is installed and working correctly by running a Docker command, such as:

   ```bash
   docker --version
   ```

   This command should display the Docker version if it's installed correctly.

6. **Run the Jenkins job again**: Once Docker is installed and verified, rerun the Jenkins job that builds the Docker image. It should now be able to find the Docker command and build the image successfully.

By following these steps, you should be able to ensure that Docker is properly installed and configured on your Azure VM running Jenkins.
```

