<img src="https://github.com/kura-labs-org/kuralabs_deployment_1/blob/main/Kuralogo.png">
# Kura Labs Deployment 5
  
## Utilize a Jenkins server on a default VPC and Jenkins agents on a Terraform server and a Docker server to deploy a containerized url-shortner Flask application with Amazon Elastic Container Service (ECS).

1. Install Jenkins on an EC2 in default VPC; the Jenkins manager will be on this EC2 and will relay duties to Jenkins agents in other EC2s when project pipeline is built. 

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

2. Install Terraform on an EC2 in default VPC and install Docker on an EC2 in default VPC. We are utilizing separate EC2s to illuminate a real application environment which would have various stages of the pipeline be dispersed for security and resource contention purposes.

- Run the same script as did in the Jenkins server on both EC2s to install packages needed for Jenkins agents that will be on both EC2s.

```diff
- Issue:
- Ubuntu user in Docker server doesn't have permission to use docker; had to add user to the Docker group and relaunch Docker agent from Jenkins.
```
```
sudo usermod -a G docker ubuntu
```
![d5 error1](https://user-images.githubusercontent.com/108698688/201431779-014cecb7-f80d-49ea-9645-605664cabc91.jpg)

3. Configure Jenkins by navigating to http://PublicIPv4:8080.

- Add Docker plugins: Docker plugin and Docker pipeline. These plugins will be referenced in Jenkinsfile and integrate Jenkins with Docker and build and use docker containers from the Jenkins pipeline.

- Add Docker Hub global credentials with username and password. This will give Jenkins access to Docker Hub repository that referenced image to build container from.

- Create two Jenkins agent profiles/nodes for the Docker server and Terraform server that will launch agents via SSH and only build jobs with label expressions matching the nodes in the Jenkinsfile. 

4. Create Jenkinsfile with Build, Test, Create Container, Push to Docker Hub, Terraform Init, Terraform Plan and Terraform Apply stages. Within the steps will clarify if a agent needs to be called, so the Jenkins manager will direct those agents to do respective stages of the pipeline in the EC2s they are on.

5. Modify Terraform files to reflect Docker image location in Docker Hub (which will be used to make the container with Terraform as seen in main.tf) and add IAM ARN role that will allow ECS tasks to call AWS services. Terraform will create a ALB (to connect subnets to interna, a ECS cluster of containers in a VPC with 2 public and 2 private subnets, a nat gateway, route tables, internet gateway.????  

![D5 cluster](https://user-images.githubusercontent.com/108698688/201431751-73fd6fcc-a48d-473d-bbb6-65f45c5f5490.jpg)
![D5 success](https://user-images.githubusercontent.com/108698688/201431729-25d601e0-fae7-4268-a305-bee8c341038e.jpg)
