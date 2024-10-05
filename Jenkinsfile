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
        stage('Checkout the voting-service Branch') {
            steps {
               // Corrected syntax for git
               //git branch: 'main', credentialsId: 'github-credentials', url: 'https://github.com/AdekunleDally/voting-app.git', timeout:30
               git branch: 'voting-service', credentialsId: 'my-github-credentials', url: 'git@github.com:AdekunleDally/voting-app.git'

            }
        }

        stage('Test') {
            steps {
                dir('voting-service') {
                    bat 'go test .' // Running Go tests in voting-service directory on Windows
                }
            }
        }

        stage('Build the voting-service Docker Image') {
            steps {
                dir('voting-service') {
                    script {
                        docker.build(DOCKER_IMAGE)
                    }
                }
            }
        }

        stage('Push the voting-service Docker Image to DockerHub') {
            steps {
                script {
                    withDockerRegistry([credentialsId: 'docker-credentials', url: 'https://registry.hub.docker.com']) {
                        bat 'docker tag "voting-service" "lukmanadeokun31/voting-service:latest"'
                        bat 'docker push "lukmanadeokun31/voting-service:latest"'
                    }
                }
            }
        }

        // stage('Deploy voting-service to Kubernetes with Helm') {
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
