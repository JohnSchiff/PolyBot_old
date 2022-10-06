node {
sh 'aws ecr get-login-password --region eu-north-1 | docker login --username AWS --password-stdin 352708296901.dkr.ecr.eu-north-1.amazonaws.com'
}


pipeline {
    agent {
        docker {
            image '352708296901.dkr.ecr.eu-north-1.amazonaws.com/alonit-jenkins-agent:latest'
            args  '--user root -v /var/run/docker.sock:/var/run/docker.sock'
        }
    }

    environment {
        APP_ENV = "dev"
    }

    parameters {
        string(name: 'BOT_IMAGE_NAME')
    }

    stages {
        stage('Bot Deploy') {
            steps {
                withCredentials([
                    string(credentialsId: 'telegram-bot-token', variable: 'TELEGRAM_TOKEN'),
                    file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')
                ]) {
                    sh '''
                    K8S_CONFIGS=infra/k8s

                    BOT_IMAGE_NAME=$(echo $BOT_IMAGE_NAME | sed -e 's/[\/&]/\\&/g')

                    # replace registry url and image name placeholders in yaml
                    sed -i "s/{{APP_ENV}}/$APP_ENV/g" $K8S_CONFIGS/bot.yaml
                    sed -i "s/{{BOT_IMAGE}}/$BOT_IMAGE_NAME/g" $K8S_CONFIGS/bot.yaml
                    sed -i "s/{{TELEGRAM_TOKEN}}/$TELEGRAM_TOKEN/g" $K8S_CONFIGS/bot.yaml

                    # apply the configurations to k8s cluster
                    kubectl apply --kubeconfig ${KUBECONFIG} -f $K8S_CONFIGS/bot.yaml
                    '''
                }
            }
        }
    }
}