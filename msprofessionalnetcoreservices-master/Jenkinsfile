pipeline {
  agent any
  stages {
    stage('Prepare Images') {
      steps {
        sh 'bash ./jenkins/scripts/prepare-image.sh'
      }
    }
    stage('Clean Prune Images') {
      steps {
        sh 'bash ./jenkins/scripts/cleanup-images.sh'
      }
    }
    stage('Push Image') {
      steps {
        sh 'bash ./jenkins/scripts/push-images.sh'
      }
    }
    stage('Run Container Local') {
      steps {
        sh 'bash ./jenkins/scripts/run-container.sh'
        input(message: 'Have you completed Testing the Container Locally?', ok: 'Yes, Continue to Next Stage in the Pipeline')
        sh 'bash ./jenkins/scripts/cleanup-containers.sh'
      }
    }
    stage('AWS Deploy') {
      steps {
        emailext(subject: '${JOB_NAME} - ${BUILD_ID} - Approval Task', body: 'The pipeline for ${JOB_NAME} has completed pre-processing tasks successfully, needs to be approved before it\'s being deployed to AWS environment.  To continue approval, click <a href=\'${BUILD_URL}\'> here </a>  Logs:  ${BUILD_LOG}', to: 'jd.ramkumar@gmail.com')
        input(message: 'Would you like to continue deploying to AWS ECS Cluster?', ok: 'Yes, Please!')
        sh 'bash ./jenkins/scripts/aws-deploy.sh'
      }
    }
  }
  environment {
    HOME = '.'
    DOCKER_HUB_USER = 'iomega'
    DOCKER_HUB_PASSWORD = 'Prestige'
    CONTAINER_PORT = '9090'
    HOST_PORT = '9090'
    DB_CONN_STRING = 'ZGF0YSBzb3VyY2U9aW9tZWdhc3Fsc2VydmVydjIuZGF0YWJhc2Uud2luZG93cy5uZXQ7dXNlciBpZD1pb21lZ2FhZG1pbjtwYXNzd29yZD1QcmVzdGlnZTEyMztpbml0aWFsIGNhdGFsb2c9aW9tZWdhc3FsZGF0YWJhc2V2Mjs='
    CONTAINER_NAME = 'restservices'
    CLUSTER_NAME = 'iomega-cluster'
  }
}