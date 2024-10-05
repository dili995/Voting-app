pipeline { 
    agent any

    tools {
        go 'go-1.20'
    }

    environment {
        GO111MODULE = 'on'
    }

    stages {
        stage('Checkout postgres Code') {
            steps {
               // Clone the repository without specifying a branch or path
               git branch: 'postgres', credentialsId: 'my-github-credentials', url: 'git@github.com:AdekunleDally/voting-app.git'

            }
        }

        stage('Build the Postgres Image') {
            steps {
                dir('postgres'){
                    script {
                         bat 'docker build -t "postgres" .'                  
                    }
                }
            }
        }

        stage('Push postgres Image') {
            steps {
                script {
                    withDockerRegistry([credentialsId: 'docker-credentials', url: 'https://registry.hub.docker.com']) {
                        bat 'docker tag "postgres" "lukmanadeokun31/postgres:latest"'
                        bat 'docker push "lukmanadeokun31/postgres:latest"'
                    }
                }
            }
        }
    }
}
