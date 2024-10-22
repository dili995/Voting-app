pipeline { 
    agent any

    tools {
        go 'go-1.20'
    }

    environment {
        GO111MODULE = 'on'
        KUBECONFIG = credentials('kubeconfig-kind') // Using kubeconfig
        DOCKER_IMAGE = "lukmanadeokun31/redis:latest"
        RELEASE_NAME = "redis"
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
                bat "helm upgrade --install ${RELEASE_NAME} ./redis-chart -f ./redis-chart/values.yaml --kubeconfig=${KUBECONFIG} --set image.repository=${DOCKER_IMAGE} --set image.tag=\"latest\""     
            }
        }

        stage('Test Deployment') {
            steps {
                bat 'kubectl get pods'
            }
        }
    }

    post {
        failure {
            script {
                // Rollback logic for failed deployment
                bat "helm rollback ${RELEASE_NAME}"
            }
        }
        always {
            cleanWs() // Clean workspace after build
        }
    }
}
