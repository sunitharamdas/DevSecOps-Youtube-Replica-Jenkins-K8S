# DevSecOps-Youtube-App-Jenkins-K8S
Youtube Clone app with DevSecOps, Kubernetes, Docker, SonarQube, Trivy, OWASP Dependency Check, Prometheus, Grafana, Jenkins (and a shared library), Splunk, Rapid API, Slack notifications

##  Step 1: Launch an Ubuntu 22.04 instance for Jenkins
Log into AWS Console: Sign in to your AWS account.

Launch an Instance:

Choose “EC2” from services. Click “Launch Instance.”

Choose an AMI: Select an Ubuntu image.

Choose an Instance Type: Pick “t2.large.”

Key Pair: Choose an existing key pair or create a new one.

Configure Security Group:

Create a new security group. Add rules for HTTP, and HTTPS, and open all ports for learning purposes. Add Storage: Allocate at least 20 GB of storage.

Launch Instance: Review and launch the instance.
Create a virtual environment
```
python3 -m venv .venv
source .venv/bin/activate
```
and setup aws configure with the IAM user aws_access_key and aws_secret_key.
Access Your Instance: Use SSH to connect to your instance with the private key.

Keep in mind, that opening all ports is not recommended for production environments; it’s just for educational purposes.
Connect to Your EC2 Instance and Install Jenkins:
```
sudo vi install_jenkins.sh
```
```
#!/bin/bash

set -e

# Update system packages
echo "Updating system packages..."
sudo apt update -y
sudo apt upgrade -y

# Check if a newer kernel is installed
current_kernel=$(uname -r)
latest_kernel=$(apt-cache search linux-image-$(uname -r | cut -d'-' -f1) | grep "aws" | sort -V | tail -1 | awk '{print $1}' | cut -d'-' -f3-)

echo "Current Kernel: $current_kernel"
echo "Latest Kernel: $latest_kernel"

# If a newer kernel is installed, reboot the system
if [ "$current_kernel" != "$latest_kernel" ]; then
    echo "A new kernel version is available. Rebooting the system..."
    sudo reboot
    exit
else
    echo "The system is already running the latest kernel."
fi

# Install Java SDK 11
echo "Installing OpenJDK 11..."
sudo apt install -y openjdk-11-jdk

# Download and Install Jenkins
echo "Installing Jenkins..."
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
sudo apt update -y
sudo apt install -y jenkins

# Start Jenkins service
echo "Starting Jenkins service..."
sudo systemctl start jenkins

# Enable Jenkins to start on boot
sudo systemctl enable jenkins

# Check Jenkins status
sudo systemctl status jenkins

echo "Jenkins installation completed!"
```

Save and exit the text editor.

Make the script executable:
```
sudo chmod +x install_jenkins.sh
```
Run the script:
```
./install_jenkins.sh
```
The script will install Jenkins and start the Jenkins service.

You will need to go to your AWS EC2 Security Group and open Inbound Port 8080 since Jenkins works on Port 8080.

Now, grab your Public IP Address
```
EC2 Public IP Address:8080
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```
## Step 2 Install Docker on the Jenkins machine
```
sudo apt-get update
sudo apt-get install docker.io -y
sudo usermod -aG docker $USER   #my case is ubuntu
newgrp docker
sudo chmod 777 /var/run/docker.sock
```
After the docker installation, we will create a Sonarqube container (Remember to add 9000 ports in the security group).

Run this command on your EC2 instance to create a SonarQube container:


```
docker run -d --name sonar -p 9000:9000 sonarqube:lts-community
```
To access SonarQube
```
ec2-public-ip:9000
```
## Step 3: Install Trivy on Jenkins machine
Create a shell script
```
sudo vi trivy.sh
sudo apt-get install wget apt-transport-https gnupg lsb-release -y
wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | gpg --dearmor | sudo tee /usr/share/keyrings/trivy.gpg > /dev/null
echo "deb [signed-by=/usr/share/keyrings/trivy.gpg] https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main" | sudo tee -a /etc/apt/sources.list.d/trivy.list
sudo apt-get update
sudo apt-get install trivy -y
```
Provide executable permissions and run the shell script

```
sudo chmod +x trivy.sh
./trivy.sh
```
This will install Trivy on our Jenkins machine.

