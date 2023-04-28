/* import shared library */

@Library('shared-library') _

pipeline {
  environment {
    IMAGE_NAME="ic-webapp"
    IMAGE_TAG = "latest"
    STAGING = "ic-webapp-mb-staging"
    PRODUCTION="ic-webapp-mb-prod"
    DOCKERHUB_ID = "mnberthe"
    DOCKERHUB_PASSWORD = credentials('dockerhub')
    HEROKU_API_KEY  = credentials('heroku_api_key')
    INTERNAL_PORT = "8080"
    EXTERNAL_PORT = "9090"
    CONTAINER_IMAGE = "${DOCKERHUB_ID}/${IMAGE_NAME}:${IMAGE_TAG}"
    ODOO_URL= "https://www.odoo.com/"
    PGADMIN_URL= "https://www.pgadmin.org/"
  }
  agent any
  
  stages {
    stage('Build image'){
      agent any
      steps {
        script {
          sh 'docker build -t $DOCKERHUB_ID/$IMAGE_NAME:$IMAGE_TAG .'
        }
      }
    }
    stage('Run container based on builded image') {  
      agent any
      steps {
        script {
          sh '''
              echo "Cleaning existing container if exist"
              docker ps -a | grep -i $IMAGE_NAME && docker rm -f $IMAGE_NAME
              docker run -d -p $EXTERNAL_PORT:$INTERNAL_PORT -e ODOO_URL=$ODOO_URL -e PGADMIN_URL=$PGADMIN_URL --name $IMAGE_NAME  ${DOCKERHUB_ID}/$IMAGE_NAME:$IMAGE_TAG
              sleep 5
          '''
         }
      }
    }

  }
  
  post {
    always {
      script {
        slackNotifier currentBuild.result
      }
    }
  }
} 
