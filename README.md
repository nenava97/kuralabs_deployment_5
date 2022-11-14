<img src="https://github.com/kura-labs-org/kuralabs_deployment_1/blob/main/Kuralogo.png">
#Kura Labs Deployment 5
  
## Utilize a Jenkins server on a default VPC utilize Jenkins agents on a Terraform server and a Docker server to deploy a containerized url-shortner Flask application with Amazon elastic service.

1. Install Jenkins on an EC2 in default VPC

- Run the following script in EC2 CLI to install packages needed for Jenkins manager.
```
#!/bin/bash
sudo apt update
sudo apt install python3.10-venv
sudo apt -y install openjdk-11-jre
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo tee \ /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \ https://pkg.jenkins.io/debian-stable binary/ | sudo tee \ /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get -y install jenkins
sudo systemctl enable jenkins
sudo systemctl start jenkins
```
- Configure Jenkins by navigating to http://PublicIPv4:8080.

- Add Docker global credentials for awsaccess key and aws secret key.

2. Install Terraform on an EC2 in default VPC and install Docker on an EC2 in default VPC

- Run the same script as did in the Jenkins server to install packages needed for Jenkins agents that will be on both EC2s.

3. 

![d5 error1](https://user-images.githubusercontent.com/108698688/201431779-014cecb7-f80d-49ea-9645-605664cabc91.jpg)
![D5 container creation](https://user-images.githubusercontent.com/108698688/201431761-35b18892-aac6-4ff0-8208-55e14646e2a8.jpg)
![D5 cluster](https://user-images.githubusercontent.com/108698688/201431751-73fd6fcc-a48d-473d-bbb6-65f45c5f5490.jpg)
![D5 success](https://user-images.githubusercontent.com/108698688/201431729-25d601e0-fae7-4268-a305-bee8c341038e.jpg)
