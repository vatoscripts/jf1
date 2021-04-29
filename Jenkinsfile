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

    stage('Deploy E2E') {
      environment {
        GIT_CREDS = credentials('GIT')
      }
      steps {
        
          sh "git clone https://github.com/vatoscripts/argocd-demo-deploy.git"
          sh "git config --global user.email 'vatoscripts@gmail.com'"

          dir("argocd-demo-deploy") {
            sh "cd ./e2e && kustomize edit set image kiyange26773/jf1:${env.GIT_COMMIT}"
            sh "git commit -am 'Publish new version' && git push || echo 'no changes'"
          }
        
      }
    }

    }
  }

