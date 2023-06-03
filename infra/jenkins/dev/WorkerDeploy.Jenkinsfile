pipeline {
    agent {
        docker {
            // TODO build & push your Jenkins agent image, place the URL here
            image '700935310038.dkr.ecr.eu-north-1.amazonaws.com/of1r-jenkinsagent:1'
            args  '--user root -v /var/run/docker.sock:/var/run/docker.sock'
        }
    }

    environment {
        APP_ENV = "dev"
    }

    parameters {
        string(name: 'WORKER_IMAGE_NAME')
    }


   stages {

        stage('yaml build'){
            steps {
                sh "sed -i 's|WORKER_IMAGE|$WORKER_IMAGE_NAME|g' infra/k8s/worker.yaml"

            }
        }
        stage('Bot Deploy') {
            steps {

                withCredentials([
                    file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')
                ]) {

                    sh '''

                    # apply the configurations to k8s cluster

                     kubectl apply --kubeconfig ${KUBECONFIG} -f infra/k8s/worker.yaml --namespace dev

                    '''
                }
            }
        }
    }
}