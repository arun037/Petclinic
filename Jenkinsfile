pipeline {
    agent any 
    
    tools{
        jdk 'jdk11'
        maven 'maven3'
    }
    
    environment {
        sshUser  = 'ubuntu'
        serverIP = '3.110.164.90' //change your tomcat server ip
        warFilePath = '/var/lib/jenkins/workspace/tomcat/target/petclinic.war'
    }
    
    stages{
        
        stage('clean ws') {
            steps {
               cleanWs()
            }
        }

         stage('git checkout') {
            steps {
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'github-token', url: 'https://github.com/arun037/Petclinic.git']])
            }
        }
        
        stage("Compile"){
            steps{
                sh "mvn clean compile"
            }
        }
        
         stage("Test Cases"){
            steps{
                sh "mvn test"
            }
        }
        
        
         stage("Build"){
            steps{
                sh " mvn package"
            }
        
       stage('copy the war file') {
            steps {
                     withCredentials([sshUserPrivateKey(credentialsId: 'tomcatssh', keyFileVariable: 'SSH_KEY')]) {
                        sh """
                        scp -o StrictHostKeyChecking=no -i $SSH_KEY $warFilePath $sshUser@$serverIp:/opt/tomcat/webapps/
                        ssh -o StrictHostKeyChecking=no -i $SSH_KEY $sshUser@$serverIp "sudo systemctl restart tomcat"

                        """
                }
            }
       }
    }
}
