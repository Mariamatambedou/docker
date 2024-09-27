pipeline {
    environment {
        imagename = "tambedou/demo-enset-student"
        registryCredential = 'Dockerhub'
        dockerImage = ''
        sonarqubeServerUrl = ' https://4b63-196-207-231-216.ngrok-free.app'
        sonarToken = credentials('sonar-token')
    }
    agent any
    stages {
        stage('Cloning Git') {
            steps {
                git([url: 'https://github.com/Mariamatambedou/docker.git', branch: 'main', credentialsId: 'Github'])
            }
        }
        stage('SonarQube Scan') {
            steps {
                script {
                    withSonarQubeEnv('SonarQube') {
                        sh """
                        sonar-scanner
                        -Dsonar.projectKey=docker
                        -Dsonar.sources=.
                        -Dsonar.host.url=${sonarqubeServerUrl}
                        -Dsonar.login=${sonarToken}
                        """
                    }
                }
            }
        }
        stage('Quality Gate') {
            steps {
                script {
                    timeout(time: 5, unit: 'MINUTES') {
                        waitForQualityGate abortPipeline: true
                    }
                }
            }
        }
        stage('Building image') {
            steps {
                script {
                    dockerImage = docker.build(imagename, ".")
                }
            }
        }
        stage('Deploy Image') {
            steps {
                script {
                    docker.withRegistry('', registryCredential) {
                        dockerImage.push("$BUILD_NUMBER")
                        dockerImage.push('latest')
                    }
                }
            }
        }
        stage('Remove Unused docker image') {
            steps {
                sh "docker rmi $imagename:$BUILD_NUMBER"
                sh "docker rmi $imagename:latest"
            }
        }
    }
}
