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
                script {
                    bat 'docker build -t "redis" .'
                }
            }
        }

        stage('Push Redis Image') {
            steps {
                script {
                    withDockerRegistry([credentialsId: 'docker-credentials', url: 'https://registry.hub.docker.com']) {
                        bat 'docker tag "redis" "lukmanadeokun31/redis:latest"'
                        bat 'docker push "lukmanadeokun31/redis:latest"'
                    }
                }
            }
        }

        stage('Load image to KIND Cluster') {
            steps {
                bat 'kind load docker-image lukmanadeokun31/redis:latest --name votingapp-microservice'
            }
        }

        stage('Deploy with Helm') {
            steps {
                bat 'helm upgrade --install redis ./redis-chart -f ./redis-chart/values.yaml --set image.tag="latest" '
            }
        }

        stage('Test Deployment') {
            steps {
                bat 'kubectl get pods -n redis-namespace'
            }
        }
    }
}
