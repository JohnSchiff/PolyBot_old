pipeline {
    agent any

    environment {
        REGISTRY_URL= "352708296901.dkr.ecr.eu-west-2.amazonaws.com"
        IMAGE_TAG= "$BUILD_NUMBER"
        IMAGE_NAME = "schiff-jenkins-ex1"
        REGION = "eu-west-2"
    }

    stages {
        stage('Echoing'){
        steps{
            sh '''
            pwd
            cd /var/lib/jenkins/workspace/BotBuild/infra/jenkins
            echo Now we are in 
            pwd
            '''
        }
        }

        stage('Build Jenkins Agent Docker') {
            steps {
                sh'''
                cd 
                docker build -t $IMAGE_NAME:$IMAGE_TAG -f JenkinsAgent.Dockerfile /infra/jenkins
                docker tag $IMAGE_NAME:$IMAGE_TAG $REGISTRY_URL/$IMAGE_NAME:$IMAGE_TAG 
                 
                 '''
                }
        }

    }
}