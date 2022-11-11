pipeline {
  agent any
   stages {
    stage ('Build') {
      steps {
        sh '''#!/bin/bash
        python3 -m venv test3
        source test3/bin/activate
        pip install pip --upgrade
        pip install -r requirements.txt
        export FLASK_APP=application
        flask run &
        '''
     }
   }
    stage ('test') {
      steps {
        sh '''#!/bin/bash
        source test3/bin/activate
        py.test --verbose --junit-xml test-reports/results.xml
        ''' 
      }

      post{
        always {
          junit 'test-reports/results.xml'
        }

      }
    }
    stage ('create container') {
      agent{label 'DockerAgent'}
      steps {
        script {
            app = docker.build("nenava97/urlshort")
        }
      }
    }
    stage('Push to Dockerhub') {
     agent{label 'DockerAgent'}
     steps {
        script {
            docker.withRegistry('https://registry.hub.docker.com', 'Docker_Hub_Login') {            
            app.push("${env.BUILD_NUMBER}")            
            app.push("latest")
            }
        }
     }
     stage('Init') {
       agent{label 'TerraformAgent'}
       steps {
        withCredentials([string(credentialsId: 'AWS_ACCESS_KEY', variable: 'aws_access_key'), 
                        string(credentialsId: 'AWS_SECRET_KEY', variable: 'aws_secret_key')]) {
                            dir('intTerraform') {
                              sh 'terraform init' 
                            }
         }
    }
     }
      stage('Plan') {
       agent{label 'TerraformAgent'} 
       steps {
        withCredentials([string(credentialsId: 'AWS_ACCESS_KEY', variable: 'aws_access_key'), 
                        string(credentialsId: 'AWS_SECRET_KEY', variable: 'aws_secret_key')]) {
                            dir('intTerraform') {
                              sh 'terraform plan -out plan.tfplan -var="aws_access_key=$aws_access_key" -var="aws_secret_key=$aws_secret_key"' 
                            }
         }
    }
   }
    }
      stage('Apply') {
       agent{label 'TerraformAgent'}
       steps {
        withCredentials([string(credentialsId: 'AWS_ACCESS_KEY', variable: 'aws_access_key'), 
                        string(credentialsId: 'AWS_SECRET_KEY', variable: 'aws_secret_key')]) {
                            dir('intTerraform') {
                              sh 'terraform apply plan.tfplan' 
                            }
         }
    }
   }
 }
}
