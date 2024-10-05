pipeline { 
    agent any

    tools {
        go 'go-1.20'
    }

    environment {
        GO111MODULE = 'on'
    }

    stages {
        stage('Checkout Redis Code') {
            steps {
               // Clone the repository without specifying a branch or path
               git branch: 'redis', credentialsId: 'my-github-credentials', url: 'git@github.com:AdekunleDally/voting-app.git'

            }
        }

        stage('Build redis Image') {
            steps {
                dir('redis') {
                    script {
                        bat 'docker build -t "lukmanadeokun31/redis" .'
                    }
                }
            }
        }

        stage('Push Redis Image') {
            steps {
                script {
                    withDockerRegistry([credentialsId: 'docker-credentials', url: 'https://registry.hub.docker.com']) {
                        bat 'docker tag "lukmanadeokun31/redis" "lukmanadeokun31/redis:latest"'
                        bat 'docker push "lukmanadeokun31/redis:latest"'
                    }
                }
            }
        }
    }
}
