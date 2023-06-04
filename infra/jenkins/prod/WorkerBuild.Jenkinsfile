pipeline {
    agent {
        docker {
            // TODO build & push your Jenkins agent image, place the URL here
            image '700935310038.dkr.ecr.eu-north-1.amazonaws.com/of1r-jenkinsagent:1'
            args  '--user root -v /var/run/docker.sock:/var/run/docker.sock'
        }
    }

    environment {
    REGISTRY_URL = '700935310038.dkr.ecr.us-west-2.amazonaws.com'
    IMAGE_NAME = 'matan-prod-worker'
    IMAGE_TAG = '${BUILD_NUMBER}'

    }

    stages {
        stage('Build') {
            steps {
                // TODO dev worker build stage
                sh '''
                aws ecr get-login-password --region us-west-2 | docker login --username AWS --password-stdin $REGISTRY_URL
                docker build -t $IMAGE_NAME:$BUILD_NUMBER -f worker/Dockerfile .
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

    }
}