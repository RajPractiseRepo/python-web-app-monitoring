pipeline {
    agent any
    
     tools {
        jdk 'jdk17'
    
    }
    
    
    environment {
        SCANNER_HOME=tool 'sonar-scanner'
    } 

    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/RajPractiseRepo/python-web-app-monitoring.git'
            }
        }
        
         stage("OWASP Dependency"){
            steps{
                dependencyCheck additionalArguments: '--scan ./ ', odcInstallation: 'DC'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            } 
        }
        
        stage("Trivy FS Scan"){
            steps{
                sh "trivy fs ."
            }
        } 
        
        
        stage(" Sonarqube Analysis "){
            steps{
                 withSonarQubeEnv('sonar') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Python-webapp \
                    -Dsonar.projectKey=Python-webapp '''
                    
                 }
            }
        } 
        
        stage("Docker Build and Tag"){
            steps{
                script{
                    
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        
                        sh "make image"
   
                    }
                    
                }
            }
        }
        
        
         stage("Trivy Image Scan"){
            steps{
                sh "trivy image rajpractise/python-webapp:latest"
            }
        } 
        
        
        stage("Docker Push Image"){
            steps{
                script{
                    
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        
                        sh "make push"
   
                    }
                    
                }
            }
        }
        
        
        stage("Deploy To Docker Container"){
            steps{
                script{
                    
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        
                        sh "docker run -d -p 5000:5000 rajpractise/python-webapp:latest"
   
                    }
                    
                }
            }
        } 
        
    }
}
