pipeline {
    agent {
        docker {
            // TODO build & push your Jenkins agent image, place the URL here
            image '700935310038.dkr.ecr.eu-north-1.amazonaws.com/of1r-jenkinsagent:1'
            args  '--user root -v /var/run/docker.sock:/var/run/docker.sock'
        }
    }

    options {
        timestamps()
        timeout(time: 30, unit: 'MINUTES')

    }
    environment {
        IMAGE_NAME = 'of1r-bot-prod'
        IMAGE_TAG = "${BUILD_NUMBER}"
        REPO_URL = '700935310038.dkr.ecr.eu-north-1.amazonaws.com'
    }

  stages {
        stage('ECR Login') {

            steps {

                sh 'aws ecr get-login-password --region us-west-2 | docker login --username AWS --password-stdin 700935310038.dkr.ecr.eu-north-1.amazonaws.com'
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
    }
}