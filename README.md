### Setting Up Jenkins on Azure Virtual Machine

1. **Setting up Jenkins**

    - Create a test directory called `jenkins-get-started`.

    - Switch to the test directory:
    ```bash
   cd jenkins-get-started
    ```

    - Create a file named `cloud-init-jenkins.txt`. 

    - Paste the following code into the new file:
    ```yaml
   #cloud-config
   package_upgrade: true
   runcmd:
     - sudo apt install openjdk-11-jre -y
     - curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee /usr/share/keyrings/jenkins-keyring.asc > /dev/null
     -  echo 'deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/' | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
     - sudo apt-get update && sudo apt-get install jenkins -y
     - sudo service jenkins restart
    ```

    - Save the file and proceed with the Jenkins setup.


2. **Create a Virtual Machine**:
   - Run `az group create` to create a resource group.
     ```bash
     az group create --name jenkins-get-started-rg --location eastus
     ```
   - Run `az vm create` to create a virtual machine.
     ```bash
     az vm create \
     --resource-group jenkins-get-started-rg \
     --name jenkins-get-started-vm \
     --image UbuntuLTS \
     --admin-username "azureuser" \
     --generate-ssh-keys \
     --public-ip-sku Standard \
     --custom-data cloud-init-jenkins.txt
     ```
   - Run `az vm list` to verify the creation (and state) of the new virtual machine.
     ```bash
     az vm list -d -o table --query "[?name=='jenkins-get-started-vm']"
     ```
   - Open port 8080 on the new virtual machine to allow Jenkins to run.
     ```bash
     az vm open-port \
     --resource-group jenkins-get-started-rg \
     --name jenkins-get-started-vm  \
     --port 8080 --priority 1010
     ```

2. **Configure Jenkins**:
   - Run `az vm show` to get the public IP address for the sample virtual machine.
     ```bash
     az vm show \
     --resource-group jenkins-get-started-rg \
     --name jenkins-get-started-vm -d \
     --query [publicIps] \
     --output tsv
     ```
   - SSH into the virtual machine using the retrieved IP address.
     ```bash
     ssh azureuser@<ip_address>
     ```

    - In case you were asked to input a paraphrase and you dont know it. You can add SSH key to SSH agent
     ```bash
     eval "$(ssh-agent -s)"
     ssh-add ~/.ssh/id_rsa
     ```

    - After adding the SSH key to the SSH agent. you can verify that the key has been successfully added
     ```bash
     ssh-add -l
     ```
    - Check SSH agent configuration. This command should display SSH agent socket and process ID
     ```bash
     echo $SSH_AUTH_SOCK
     echo $SSH_AGENT_PID
     ```

3. **Verify that Jenkins is running by getting the status of the Jenkins service.**
    ```bash
    service jenkins status
    ```

4. **To access Jenkins on your Azure virtual machine, follow these steps:**

- Get the autogenerated Jenkins password.
    ```bash
    sudo cat /var/lib/jenkins/secrets/initialAdminPassword
    ```

- Open a web browser.

- Enter the following URL in the address bar, replacing `<ip_address>` with the public IP address of your Azure virtual machine:
   ```bash
   http://<ip_address>:8080
   ```

- Press Enter to navigate to the Jenkins login page.
- You will be prompted to enter the initial administrator password. Use the password you retrieved earlier during the Jenkins  configuration.

- After entering the password, click on the "Continue" button.

You should now have access to Jenkins on your Azure virtual machine via the web browser.



https://learn.microsoft.com/en-us/azure/developer/jenkins/configure-on-linux-vm




5. **Clean up resources**

In this module, you created Azure resources in a resource group. If you won't need these resources later, delete the resource group from the Azure portal or run the following command in Azure Cloud Shell:

az group delete --name jenkins-get-started-rg --yes
This command might take a minute to run.