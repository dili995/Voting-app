pipeline { 
    agent any

    tools {
        go 'go-1.20' // Ensure go-1.20 is installed in Jenkins global tools
    }

    environment {
        GO111MODULE = 'on'
        DOCKER_IMAGE = "lukmanadeokun31/results-service"
        KUBECONFIG = credentials('kubeconfig-kind') 
        RELEASE_NAME = "results"
    }

    stages {
        stage('Checkout the results-service Branch') {
            steps {
               // Corrected syntax for git
               //git branch: 'main', credentialsId: 'github-credentials', url: 'https://github.com/AdekunleDally/voting-app.git', timeout:30
               git branch: 'results-service', credentialsId: 'my-github-credentials', url: 'git@github.com:AdekunleDally/voting-app.git'

            }
        }

        stage('Test') {
            steps {
                
                bat 'go test .' // Running Go tests in results-service directory on Windows
                
            }
        }

        stage('Build the results-service Docker Image') {
            steps {
               
                // Using Jenkins 'withCredentials' to handle the .env file securely
                withCredentials([file(credentialsId: 'results-service-env', variable: 'ENV_FILE')]) {

                // Use 'bat' to run Windows commands instead of 'sh'
                bat 'copy %ENV_FILE% .env'  // Windows equivalent of 'cp' command

                // Build the Docker image using the Windows-friendly command
                bat 'docker build -t results-service .'
                }  
            }
        }

        stage('Push the results-service Docker Image to DockerHub') {
            steps {
                script {
                    withDockerRegistry([credentialsId: 'docker-credentials', url: 'https://registry.hub.docker.com']) {
                        bat 'docker tag "results-service" "lukmanadeokun31/results-service:latest"'
                        bat 'docker push "lukmanadeokun31/results-service:latest"'
                    }
                }
            }
        }
        
        stage('Load image to KIND Cluster') {
            steps {
                bat 'kind load docker-image lukmanadeokun31/results-service:latest --name votingapp-microservice'
            }
        }

        stage('Deploy with Helm') {
            steps {
                 bat "helm upgrade --install ${RELEASE_NAME} ./results-chart -f ./results-chart/values.yaml --kubeconfig=${KUBECONFIG} --set image.repository=${DOCKER_IMAGE} --set image.tag=\"latest\""
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
