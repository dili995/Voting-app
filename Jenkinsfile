pipeline { 
    agent any

    tools {
        go 'go-1.20'
    }

    environment {
        GO111MODULE = 'on'
        DOCKER_IMAGE = "lukmanadeokun31/postgres"
        //KUBECONFIG = credentials('kubeconfig-kind')
    }

    stages {
        stage('Checkout postgres Code') {
            steps {
               // Clone  repository without specifying a branch or path
               git branch: 'postgres', credentialsId: 'my-github-credentials', url: 'git@github.com:AdekunleDally/voting-app.git'
            }
        }

        stage('Build the Postgres Image') {
            steps {
                dir('postgres'){
                    script {
                        docker.build(DOCKER_IMAGE)                   
                    }
                }
            }
        }

        stage('Push the Postgres Image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker-credentials') {
                        docker.image("${DOCKER_IMAGE}").push()   // Push with build number tag
                        //  docker.image("${DOCKER_IMAGE}").tag('latest')  // Tag as 'latest'
                    }
                }
            }
        }

        // stage('Deploy postgres to Kubernetes with Helm') {
        //     steps {
        //         script {
        //             sh """
        //             helm upgrade --install postgres ./postgres/postgres-chart \
        //                 --set image.repository=${DOCKER_IMAGE} \
        //                 --namespace postgres \
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
    //             helm rollback postgres
    //             """
    //         }
    //     }
    //     always {
    //         cleanWs() // Clean workspace after build
    //     }
    // }
}