pipeline { 
    agent any

    tools {
        go 'go-1.20' // Ensure go-1.20 is installed in Jenkins global tools
    }

    environment {
        GO111MODULE = 'on'
        DOCKER_IMAGE = "lukmanadeokun31/results-service"
        KUBECONFIG = credentials('kubeconfig-kind') 
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
                 bat "helm upgrade --install results ./results-service/results-chart -f ./results-service/results-chart/values.yaml --kubeconfig=${KUBECONFIG} --set image.repository=${DOCKER_IMAGE} --set image.tag=\"latest\""
            }
        }

        stage('Test Deployment') {
            steps {
                bat 'kubectl get pods'
            }
        }
        // stage('Deploy results-service to Kubernetes with Helm') {
        //     steps {
        //         script {
        //             sh """
        //             helm upgrade --install results ./results-service/results-chart \
        //                 --set image.repository=${DOCKER_IMAGE} \
        //                 --namespace results \
        //                 --kubeconfig $KUBECONFIG
        //             """
        //         }
        //     }
        // }
    }
    // post {
    //     failure {
    //         script {
    //             // Rollback logic for failed deployment
    //             sh """
    //             helm rollback ${env.SERVICE_NAME}
    //             """
    //         }
    //     }
    //     always {
    //         cleanWs() // Clean workspace after build
    //     }
    // }
}
