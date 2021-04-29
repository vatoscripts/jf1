pipeline {
  agent any
  stages {

    stage('Build') {
      environment {
        DOCKERHUB_CREDS = credentials('DH')
      }
      steps {
        
          // Build new image
          sh "until docker ps; do sleep 3; done && docker build -t kiyange26773/jf1:${env.GIT_COMMIT} ."
          // Publish new image
          sh "docker login --username $DOCKERHUB_CREDS_USR --password $DOCKERHUB_CREDS_PSW && docker push kiyange26773/jf1:${env.GIT_COMMIT}"
        
      }

    }
  }
}
