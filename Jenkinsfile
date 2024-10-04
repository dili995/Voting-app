pipeline {
    agent any
    
    environment {
        DOCKER_HUB_CREDENTIALS = credentials('docker-credentials')
        GITHUB_CREDENTIALS = credentials('my-github-credentials')
        KUBECONFIG_FILE = credentials('kubeconfig-kind') // Kubeconfig credentials for Kubernetes
        IMAGE_NAME = "lukmanadeokun31/redis" // Define the image name as a variable for reusability
        IMAGE_TAG = "latest" // You can change the tag dynamically or use 'latest'
    }
    
    stages {
        stage('Checkout Redis Code') {
            steps {
                git credentialsId: 'my-github-credentials', url: 'git@github.com:AdekunleDally/voting-app.git', branch: 'redis'
            }
        }
        
        stage('Build the Redis Image') {
            steps {
                dir('redis') {
                    script {
                        // Build the Redis Docker image without tagging yet
                        bat "docker build -t ${IMAGE_NAME} ."
                    }
                }
            }
        }
        
        stage('Tag and Push the Redis Image') {
            steps {
                script {
                    withDockerRegistry([credentialsId: 'docker-credentials', url: 'https://registry.hub.docker.com']) {
                        // Tagging the built image with the desired tag and pushing it to Docker Hub
                        bat "docker tag ${IMAGE_NAME}:latest ${IMAGE_NAME}:${IMAGE_TAG}"
                        bat "docker push ${IMAGE_NAME}:${IMAGE_TAG}"
                    }
                }
            }
        }

        // stage('Deploy Redis to Kubernetes with Helm') {
        //     steps {
        //         script {
        //             withCredentials([file(credentialsId: 'kubeconfig-kind', variable: 'KUBECONFIG_PATH')]) {
        //                 // Using the KUBECONFIG_PATH environment variable to deploy via Helm
        //                 bat """
        //                     helm upgrade --install redis ./redis/redis-chart \
        //                     --set image.repository=${IMAGE_NAME} \
        //                     --set image.tag=${IMAGE_TAG} \
        //                     --kubeconfig %KUBECONFIG_PATH%
        //                 """
        //             }
        //         }
        //     }
        // }
    }
}
