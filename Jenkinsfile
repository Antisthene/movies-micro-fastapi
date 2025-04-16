pipeline {
  environment { // Declaration of environment variables
    DOCKER_ID = "al38984" // replace this with your docker-id
      DOCKER_IMAGE = "jenkins-cast-fastapi"
      BUILD_ID = "1.2"
      DOCKER_TAG = "v${BUILD_ID}.0" // we will tag our images with the current build in order to increment the value by 1 with each new build
  }
  agent any // Jenkins will be able to select all available agents
    stages {
      stage(' Docker Build'){ // docker build image stage
        when {
expression {env.GIT_BRANCH == 'origin/dev'}
        }
        steps {
          script {
            sh '''
              cd jenkins_devops_exams/movie-service/
              docker rm -f jenkins
              docker build -t $DOCKER_ID/$DOCKER_IMAGE:$DOCKER_TAG .
              sleep 6
              '''
          }
        }
      }
      stage('Docker run'){ // run container from our builded image
        when {
expression {env.GIT_BRANCH == 'origin/dev'}
        }
        steps {
          script {
            sh '''
              docker run -d -p 5002:5002/tcp --name jenkins $DOCKER_ID/$DOCKER_IMAGE:$DOCKER_TAG
              sleep 10
              '''
          }
        }
      }
      stage('Docker Push'){ //we pass the built image to our docker hub account
        environment
        {
          DOCKER_PASS = credentials("DOCKER_HUB_PASS") // we retrieve  docker password from secret text called docker_hub_pass saved on jenkins
        }
        when {
expression {env.GIT_BRANCH == 'origin/dev'}
        }

        steps {

          script {
            sh '''
              docker login -u $DOCKER_ID -p $DOCKER_PASS
              docker push $DOCKER_ID/$DOCKER_IMAGE:$DOCKER_TAG
              '''
          }
        }

      }

      stage('dev deploy'){
        environment
        {
          KUBECONFIG = credentials("config") // we retrieve  kubeconfig from secret file called config saved on jenkins
        }
        when {
expression {env.GIT_BRANCH == 'origin/dev'}
        }
        steps {
          script {
            sh '''
              cd k8s
              helm upgrade --install cast-chart ./cast --values=./cast/values.yaml -n dev
              helm upgrade --install movie-chart ./movie --values=./movie/values.yaml -n dev
              helm upgrade --install nginx-chart ./nginx --values=./nginx/values.yaml -n dev
              sleep 10
              '''
          }
        }

      }

      stage('test dev deploy'){
        environment
        {
          KUBECONFIG = credentials("config") // we retrieve  kubeconfig from secret file called config saved on jenkins
        }
        when {
expression {env.GIT_BRANCH == 'origin/dev'}
        }
        steps {
          script {
            sh '''
              kubectl get po,svc,deploy,pvc,secret,configmap -n dev
              '''
          }
        }

      }


      stage('staging deploy'){
        environment
        {
          KUBECONFIG = credentials("config") // we retrieve  kubeconfig from secret file called config saved on jenkins
        }
        when {
expression {env.GIT_BRANCH == 'origin/staging'}
        }
        steps {
          script {
            sh '''
              cd k8s
              helm upgrade --install cast-chart ./cast --values=./cast/values.yaml -n staging
              helm upgrade --install movie-chart ./movie --values=./movie/values.yaml -n staging
              helm upgrade --install nginx-chart ./nginx --values=./nginx/values.yaml -n staging
              sleep 10
              '''
          }
        }

      }

      stage('qa deploy'){
        environment
        {
          KUBECONFIG = credentials("config") // we retrieve  kubeconfig from secret file called config saved on jenkins
        }
        when {
expression {env.GIT_BRANCH == 'origin/qa'}
        }
        steps {
          script {
            sh '''
              cd k8s
              helm upgrade --install cast-chart ./cast --values=./cast/values.yaml -n qa
              helm upgrade --install movie-chart ./movie --values=./movie/values.yaml -n qa
              helm upgrade --install nginx-chart ./nginx --values=./nginx/values.yaml -n qa
              sleep 10
              '''
          }
        }

      }

      stage('prod deploy'){
        environment
        {
          KUBECONFIG = credentials("config") // we retrieve  kubeconfig from secret file called config saved on jenkins
        }
        when {
expression {env.GIT_BRANCH == 'origin/master'}
        }
        steps {
          script {
            sh '''
              cd k8s
              helm upgrade --install cast-chart ./cast --values=./cast/values.yaml -n prod
              helm upgrade --install movie-chart ./movie --values=./movie/values.yaml -n prod
              helm upgrade --install nginx-chart ./nginx --values=./nginx/values.yaml -n prod
              '''
          }
        }

      }

    }
}
