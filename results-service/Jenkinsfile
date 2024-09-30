pipeline { 
    agent any

    tools {
        go 'go-1.20' // Ensure go-1.20 is installed in Jenkins global tools
    }

    environment {
        GO111MODULE = 'on'
        DOCKER_IMAGE = "lukmanadeokun31/results-service:${env.BUILD_NUMBER}"
        KUBECONFIG = credentials('kubeconfig-kind') 
    }

    stages {
        stage('Checkout the result-service Code') {
            steps {
               // Corrected syntax for git
              // git branch: 'main', credentialsId: 'github-credentials', url: 'https://github.com/AdekunleDally/voting-app.git'
                git branch: 'main', credentialsId: 'my-github-credentials', url: 'git@github.com:AdekunleDally/voting-app.git'

            }
        }

        stage('Test') {
            steps {
                dir('results-service') {
                    bat 'go test ./...' // Running Go tests in voting-service directory on Windows
                }
            }
        }

        stage('Build the result-service Docker Image') {
            steps {
                dir('results-service') {
                    script {
                        docker.build(DOCKER_IMAGE)                    
                    }
                }
            }
        }

        stage('Push the results-service Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker-credentials') {
                        docker.image("${DOCKER_IMAGE}:${env.BUILD_NUMBER}").push()   // Push with build number tag
                        docker.image("${DOCKER_IMAGE}:latest").push() 
                    }
                }
            }
        }

        // stage('Deploy results-service to Kubernetes with Helm') {
        //     steps {
        //         script {
        //             sh """
        //             helm upgrade --install results ./results/results-chart \
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