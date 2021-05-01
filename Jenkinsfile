pipeline {
    
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: dind
    image: docker:18.09-dind
    securityContext:
      privileged: true
  - name: docker
    env:
    - name: DOCKER_HOST
      value: 127.0.0.1
    image: docker:18.09
    command:
    - cat
    tty: true
  - name: tools
    image: argoproj/argo-cd-ci-builder:v0.13.1
    command:
    - cat
    tty: true
"""
    }
  }

  stages {

    stage('Build') {
      environment {
        DOCKERHUB_CREDS = credentials('DH')
      }
      steps {
        
          // Build new image
          sh "docker build -t kiyange26773/jf1:${env.GIT_COMMIT} ."
          // Publish new image
          sh "docker login --username $DOCKERHUB_CREDS_USR --password $DOCKERHUB_CREDS_PSW && docker push kiyange26773/jf1:${env.GIT_COMMIT}"
        
      }
    }
    //deploy staging
    stage('Deploy Staging') {
      environment {
        GIT_CREDS = credentials('GIT')
      }
      steps {
        
          sh "git clone https://github.com/vatoscripts/argocd-demo-deploy.git"
          sh "git config --global user.email 'vatoscripts@gmail.com'"

          dir("argocd-demo-deploy") {
            sh "cd ./e2e && ls && kustomize edit set image kiyange26773/jf1:${env.GIT_COMMIT}"
            sh "git commit -am 'Publish new version' && git push || echo 'no changes'"
          }
        
      }
    }

    //deploy to PROD
    stage('Deploy to Prod') {
      steps {
        input message:'Really Deploy?'
        
          dir("argocd-demo-deploy") {
            sh "cd ./prod && ls && kustomize edit set image kiyange26773/jf1:${env.GIT_COMMIT}"
            sh "git commit -am 'Publish new version...' && git push || echo 'no changes made...'"
          }
    
      }
    }


    }
  }

