pipeline {
    agent any
    
    environment {
        DOCKER_HUB_CREDENTIALS = credentials('docker-credentials')
        GITHUB_CREDENTIALS = credentials('my-github-credentials')
        KUBECONFIG_FILE = credentials('kubeconfig-kind') // Using the kubeconfig
    }
    
    stages {
        stage('Checkout redis Code') {
            steps {
                git credentialsId: 'my-github-credentials', url: 'git@github.com:AdekunleDally/voting-app.git', branch: 'main'
            }
        }
        
        stage('Build the Redis Image') {
            steps {
                dir('redis') {
                    script {
                        bat 'docker build -t "lukmanadeokun31/redis:19" .'
                    }
                }
            }
        }
        
        stage('Push the Redis Image') {
            steps {
                script {
                    withDockerRegistry([credentialsId: 'docker-credentials', url: 'https://registry.hub.docker.com']) {
                        bat 'docker tag "lukmanadeokun31/redis:19" "lukmanadeokun31/redis:19"'
                        bat 'docker push "lukmanadeokun31/redis:19"'
                    }
                }
            }
        }

        // stage('Deploy Redis to Kubernetes with Helm') {
        //     steps {
        //         script {
        //             // Using the KUBECONFIG_FILE environment variable directly
        //             bat '''
        //                 helm upgrade --install redis ./redis/redis-chart \
        //                 --set image.repository=lukmanadeokun31/redis \
        //                 --set image.tag=19 \
        //                 --kubeconfig %KUBECONFIG_FILE%
        //             '''
        //         }
        //     }
        // }
    }
}
