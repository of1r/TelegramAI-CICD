pipeline {
    agent {
        docker {
            image '700935310038.dkr.ecr.eu-north-1.amazonaws.com/of1r-prod-jenkins-agent:latest'
            args  '--user root -v /var/run/docker.sock:/var/run/docker.sock'
        }
    }
    environment {
    REGISTRY_URL = '700935310038.dkr.ecr.eu-north-1.amazonaws.com'
    IMAGE_NAME = 'of1r-bot-dev'
    IMAGE_TAG = '${BUILD_NUMBER}'

    }

    stages {
        stage('Build') {
            steps {
                // TODO dev bot build stage
                // 'building' message for testing
                sh '''
                echo "building..."
                aws ecr get-login-password --region eu-north-1 | docker login --username AWS --password-stdin $REGISTRY_URL
                docker build -t $IMAGE_NAME:$BUILD_NUMBER -f bot/Dockerfile .
                docker tag $IMAGE_NAME:$BUILD_NUMBER $REGISTRY_URL/$IMAGE_NAME:$BUILD_NUMBER
                docker push $REGISTRY_URL/$IMAGE_NAME:$BUILD_NUMBER
                '''
            }
            post {
                always{
                    sh 'docker image prune -a --filter "until=240" --force'
                }
            }
        }

        stage('Trigger Deploy') {
            steps {
                build job: 'BotDeploy', wait: false, parameters: [
                    string(name: 'BOT_IMAGE_NAME', value: "$REGISTRY_URL/$IMAGE_NAME:$BUILD_NUMBER")
                ]
            }
        }
    }
}