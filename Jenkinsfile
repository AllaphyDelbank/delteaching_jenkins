pipeline {
    agent any  

    environment {
        PROJECT_NAME = 'test'
        REGISTRY = 'registry.digitalocean.com/delbank-1'
        IMAGE_NAME = "${REGISTRY}/${PROJECT_NAME}"
        TAG = "${env.BUILD_ID}"
        //DOCKER_USERNAME = credentials('docker-username')
        //DOCKER_PASSWORD = credentials('docker-password')
    }
        
    triggers {
        githubPush()
    }

    stages {

        stage('Checkout') {
            steps {
                sshagent(['github']) {
                    echo "---------------------GitHub Checkout started for ${PROJECT_NAME}!----------------------"
                    sh "rm -rf ${PROJECT_NAME}"
                    sh "git clone git@github.com:velac73272/${PROJECT_NAME}.git"
                    echo "---------------------GitHub Checkout finished for ${PROJECT_NAME}!---------------------"
                }
            }
        }

        stage('Build Image') {
            steps {
                script {
                    echo "---------------------Docker build for ${PROJECT_NAME} started!----------------------"

                    sh """
                        echo docker build -f ./${PROJECT_NAME}/Dockerfile -t ${IMAGE_NAME}:${TAG} ./
                    """

                    echo "---------------------Docker build for ${PROJECT_NAME} finished!---------------------"
                }
            }
        }

        stage('Push Image') {
            steps {
                sshagent(['github']) {
                    script {
                        echo "---------------------Docker login to registry.digitalocean.com started!----------------------"
                        sh " echo docker login /*{REGISTRY} -u {DOCKER_USERNAME} -p {DOCKER_PASSWORD}/*"
                        echo "---------------------Docker login finished!---------------------"
                        
                        echo "---------------------Docker push for ${PROJECT_NAME} started!----------------------"
                        sh "echo docker push ${IMAGE_NAME}:${TAG}"
                        sh "echo docker push ${IMAGE_NAME}:latest"
                        echo "---------------------Docker push finished for ${PROJECT_NAME}!---------------------"
                    }
                }
            }
        }

        stage('Remove Image(s)') {
            steps {
                echo "---------------------Removing Unused Image(s) for ${PROJECT_NAME}...----------------------"
                sh "echo docker rmi ${IMAGE_NAME}:${env.BUILD_ID}"
                sh "echo docker rmi ${IMAGE_NAME}:latest"
                echo "---------------------Unused Image(s) for ${PROJECT_NAME} removed!-------------------------"
            }
        }

        stage('Remove Origin Repository') {
            steps {
                echo "---------------------Removing Origin Repository for ${PROJECT_NAME}...--------------------"
                sh "rm -rf ${PROJECT_NAME}"
                echo "---------------------Origin Repository for ${PROJECT_NAME} removed!-------------------------"
            }
        }

        stage('Deploy') {
            steps {
                echo "---------------------Deploying ${PROJECT_NAME}...--------------------"
                sh "docker run --name hello hello-world"
                echo "---------------------Deploy with success!-------------------------"
            }
        }
    }
}
