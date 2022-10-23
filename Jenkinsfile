
pipeline {
    agent {
        docker {
            label 'general-schiff'
            image '352708296901.dkr.ecr.eu-west-2.amazonaws.com/schiff-agent-jenkins:1'
            args  '--user root -v /var/run/docker.sock:/var/run/docker.sock'
        }
    }    
    options {
        buildDiscarder(logRotator(daysToKeepStr: '30'))
        disableConcurrentBuilds()
        timestamps()

    }

    environment {
        REGISTRY_URL= "352708296901.dkr.ecr.eu-west-2.amazonaws.com"
        IMAGE_TAG = "0.0.$BUILD_NUMBER"
        IMAGE_NAME = "schiff-repo"
    }

    stages {

        stage('Build Bot app') {
            steps {
                sh '''
                aws ecr get-login-password --region eu-west-2 |  docker login --username AWS --password-stdin $REGISTRY_URL
                docker build -t $IMAGE_NAME:$IMAGE_TAG .
                '''

                withCredentials([string(credentialsId: 'snyk',variable: 'SNYK_TOKEN')]) {
                sh '''
                snyk container test $IMAGE_NAME:$IMAGE_TAG --severity-treshold=high --file=Dockerfile
                '''
                }
                sh '''
                docker tag $IMAGE_NAME $REGISTRY_URL/$IMAGE_NAME:$IMAGE_TAG
                docker push $REGISTRY_URL/$IMAGE_NAME:$IMAGE_TAG
                '''
                }
        }
        stage('Trigger Deploy') {
            steps {
        build job: 'BotDeploy', wait: false, parameters: [
            string(name: 'BOT_IMAGE_NAME', value: "${REGISTRY_URL}/${IMAGE_NAME}:${IMAGE_TAG}")
        ]
        }
    }
    }
}
