# DevSecOps-Youtube-App-Jenkins-K8S
Youtube Clone app with DevSecOps, Kubernetes, Docker, SonarQube, Trivy, OWASP Dependency Check, Prometheus, Grafana, Jenkins (and a shared library), Splunk, Rapid API, Slack notifications

Step 1: Launch an Ubuntu 22.04 instance for Jenkins
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
