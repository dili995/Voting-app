pipeline { 
    agent any

    tools {
        go 'go-1.20' // Ensure go-1.20 is installed in Jenkins global tools
    }

    environment {
        GO111MODULE = 'on'
        DOCKER_IMAGE = "lukmanadeokun31/voting-service"
        KUBECONFIG = credentials('kubeconfig-kind') 
    }

    stages {
        stage('Checkout the worker-service Branch') {
            steps {
               // Corrected syntax for git
               //git branch: 'main', credentialsId: 'github-credentials', url: 'https://github.com/AdekunleDally/voting-app.git', timeout:30
               git branch: 'worker-service', credentialsId: 'my-github-credentials', url: 'git@github.com:AdekunleDally/voting-app.git'

            }
        }

        stage('Test') {
            steps {
                dir('worker-service') {
                    bat 'go test .' // Running Go tests in worker-service directory on Windows
                }
            }
        }

        stage('Build the worker-service Docker Image') {
            steps {
                dir('worker-service') {
                    // Using Jenkins 'withCredentials' to handle the .env file securely
                    withCredentials([file(credentialsId: 'worker-service-env', variable: 'ENV_FILE')]) {

                    // Use 'bat' to run Windows commands instead of 'sh'
                    bat 'copy %ENV_FILE% .env'  // Windows equivalent of 'cp' command

                    // Build the Docker image using the Windows-friendly command
                    bat 'docker build -t worker-service .'
                    }
                }
            }
        }

        stage('Push the worker-service Docker Image to DockerHub') {
            steps {
                script {
                    withDockerRegistry([credentialsId: 'docker-credentials', url: 'https://registry.hub.docker.com']) {
                        bat 'docker tag "worker-service" "lukmanadeokun31/worker-service:latest"'
                        bat 'docker push "lukmanadeokun31/worker-service:latest"'
                    }
                }
            }
        }

        // stage('Deploy worker-service to Kubernetes with Helm') {
        //     steps {
        //         script {
        //             sh """
        //             helm upgrade --install voting ./voting-service/voting-chart \
        //                 --set image.repository=${DOCKER_IMAGE} \
        //                 --namespace voting \
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
