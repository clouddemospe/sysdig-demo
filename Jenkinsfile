pipeline {
  agent any
  stages {
    stage('Docker Build') {
      steps {
        sh "docker build -t clouddemospe/podinfo:${env.BUILD_NUMBER} ."
      }
    }
    stage('Docker Push') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
          sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPassword}"
          sh "docker push clouddemospe/podinfo:${env.BUILD_NUMBER}"
        }
      }
    }
    stage('Docker Remove Image') {
      steps {
        sh "docker rmi clouddemospe/podinfo:${env.BUILD_NUMBER}"
      }
    }
    stage('Apply Kubernetes Files') {
      steps {
        sh "sed s/{{BUILD_NUMBER}}/$BUILD_NUMBER/g deployment.yaml $> temp.yaml" 
        sh "kubectl apply -f temp.yaml"
        sh "kubectl apply -f service.yaml"
        sh "rm -f temp.yaml"
      }
  }
}
post {
    success {
      slackSend(message: "Pipeline is successfully completed.")
    }
    failure {
      slackSend(message: "Pipeline failed. Please check the logs.")
    }
}
}
