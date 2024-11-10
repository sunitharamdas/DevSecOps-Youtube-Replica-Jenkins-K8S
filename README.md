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

Access Your Instance: Use SSH to connect to your instance with the private key.

Keep in mind, that opening all ports is not recommended for production environments; it’s just for educational purposes.
Connect to Your EC2 Instance and Install Jenkins:
```
sudo vi install_jenkins.sh
```
```
#!/bin/bash
sudo apt update -y
wget -O - https://packages.adoptium.net/artifactory/api/gpg/key/public | tee /etc/apt/keyrings/adoptium.asc
echo "deb [signed-by=/etc/apt/keyrings/adoptium.asc] https://packages.adoptium.net/artifactory/deb $(awk -F= '/^VERSION_CODENAME/{print$2}' /etc/os-release) main" | tee /etc/apt/sources.list.d/adoptium.list
sudo apt update -y
sudo apt install temurin-17-jdk -y
/usr/bin/java --version
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update -y
sudo apt-get install jenkins -y
sudo systemctl start jenkins
sudo systemctl status jenkins
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
