pipeline { 
    agent any

    tools {
        go 'go-1.20'
    }

    environment {
        GO111MODULE = 'on'
    }

    stages {
        stage('Checkout Code') {
            steps {
               // Clone the repository without specifying a branch or path
               git branch: 'redis', credentialsId: 'my-github-credentials', url: 'https://github.com/AdekunleDally/voting-app.git'
            }
        }

        stage('Build redis Image') {
            steps {
                dir('redis'){
                    script {
                        dockerImage = docker.build("lukmanadeokun31/redis")
                    }
                }
            }
        }

        stage('Push Redis Image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker-credentials') {
                        dockerImage.push('latest')
                    }
                }
            }
        }
    }
}
