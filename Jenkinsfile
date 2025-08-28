pipeline {
    agent any
    
    tools {
        jdk 'java-11'
        maven 'maven'
    }
    
    stages {
        stage('Git-checkout') {
            steps {
                git branch: 'dev', url: 'https://github.com/manjukolkar/web-application.git'
            }
        }
        
        stage('Code Compile') {
            steps {
                sh 'mvn compile'
            }
        }
        
        stage('Code Package') {
            steps {
                sh 'mvn clean install'
            }
        }
        
        stage('Build and Tag Docker Image') {
            steps {
                sh 'docker build -t ashwitha09/project-1:latest .'
            }
        }
        
        stage('Login to Docker Hub') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        sh "echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin"
                    }
                }
            }
        }
        
        stage('Push Image to Docker Hub') {
            steps {
                sh 'docker push ashwitha09/project-1:latest'
            }
        }
        
        stage('Deploy to Production') {
            steps {
                sshagent(['prod-ssh-key']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no ubuntu@13.60.61.126 '
                        docker stop webapp || true &&
                        docker rm webapp || true &&
                        docker pull ashwitha09/project-1:latest &&
                        docker run -d --name webapp -p 9000:8080 ashwitha09/project-1:latest
                    '
                    '''
                }
            }
        }
    }
}
