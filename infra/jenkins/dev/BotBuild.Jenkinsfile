pipeline {
    agent {
        docker {
            image '700935310038.dkr.ecr.eu-north-1.amazonaws.com/of1r-jenkinsagent:1'
            args  '--user root -v /var/run/docker.sock:/var/run/docker.sock'
        }
    }

    options {
        timeout(time: 30, unit: 'MINUTES')
        timestamps()
    }

    environment {
        IMAGE_NAME = 'of1r-bot-dev'
        IMAGE_TAG = "${BUILD_NUMBER}"
        REPO_URL = '7700935310038.dkr.ecr.eu-north-1.amazonaws.com'
    }

    stages {
        stage('ECR Login') {
            steps {
                sh 'aws ecr get-login-password --region eu-north-1 | docker login --username AWS --password-stdin 7700935310038.dkr.ecr.eu-north-1.amazonaws.com'
            }
        }
        stage('Image Build') {
            steps {
                sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} -f bot/Dockerfile ."
            }
        }


        stage('Image Push') {
            steps {
                sh "docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${REPO_URL}/${IMAGE_NAME}:${IMAGE_TAG}"
                sh "docker push ${REPO_URL}/${IMAGE_NAME}:${IMAGE_TAG}"
            }
            post {
                    always {
                        sh 'docker image prune -a --filter "until=64h" --force'
                    }
                }
        }


        stage('Trigger Deploy') {
            steps {
                build job: 'botDeploy', wait: false, parameters: [
                    string(name: 'BOT_IMAGE_NAME', value: "${REPO_URL}/${IMAGE_NAME}:${IMAGE_TAG}"),
                ]
            }
        }
    }
}