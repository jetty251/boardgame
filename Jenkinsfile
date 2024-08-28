pipeline {
    agent any
    
    environment {
        SCANNER_HOME= tool 'sonar-scanner'
    }

    stages {
        stage('Git') {
            steps {
                git 'https://github.com/jetty251/boardgame.git'
            }
        }
        stage('Maven') {
            steps {
                sh 'mvn clean install'
            }
        }
        stage('Sonarqube') {
            steps {
                script {
                    withSonarQubeEnv(credentialsId: 'sonarCRD') {
                        sh "$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectKey=Boardgame -Dsonar.projectName=Boardgame -Dsonar.java.binaries=."
                    }
                }
            }
        }
        stage('S3backup') {
            steps {
                sh 'aws s3 cp /var/lib/jenkins/workspace/boardgame/ s3://jetty-hotstar/boardgame/ --recursive'
            }
        }
        stage('dockerbuild') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'dockerCRD', toolName: 'docker') {
                        sh 'docker build -t jetty556677/boardgame:latest .'
                    }
                }
            }
        }
        stage('dockerdeploy') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'dockerCRD', toolName: 'docker') {
                        sh 'docker run -d -p 8083:8080 jetty556677/boardgame:latest'
                    }
                }
            }
        }
        stage('dockerpush') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'dockerCRD', toolName: 'docker') {
                        sh 'docker push jetty556677/boardgame:latest'
                    }
                }
            }
        }
    }
}
