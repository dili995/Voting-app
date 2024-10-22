pipeline { 
    agent any

    tools {
        go 'go-1.20'
    }

    environment {
        GO111MODULE = 'on'
        DOCKER_IMAGE = "lukmanadeokun31/postgres:latest"
        KUBECONFIG = credentials('kubeconfig-kind') 
        RELEASE_NAME = "postgres"
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
                script {
                    bat 'docker build -t "postgres" .'                  
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

        stage('Load image to KIND Cluster') {
            steps {
                bat 'kind load docker-image lukmanadeokun31/postgres:latest --name votingapp-microservice'
            }
        }

        stage('Deploy with Helm') {
            steps {
                bat "helm upgrade --install ${RELEASE_NAME} ./postgres-chart -f ./postgres-chart/values.yaml --kubeconfig=${KUBECONFIG} --set image.repository=${DOCKER_IMAGE} --set image.tag=\"latest\""           
            }
        }

        stage('Test Deployment') {
            steps {
                 //bat 'kubectl get pods'
                bat 'kubectl get non-existent-resource'
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
