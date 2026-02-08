pipeline {
  agent any

  environment {
    DOCKER_IMAGE = "marioerickr/exam10th-1st"
    KUBECONFIG   = "/var/lib/jenkins/.kube/config"
  }

  stages {
    stage('Checkout') {
      steps { checkout scm }
    }

    stage('Build') {
      steps {
        sh '''
          docker build -t ${DOCKER_IMAGE}:${BUILD_NUMBER} .
          docker tag ${DOCKER_IMAGE}:${BUILD_NUMBER} ${DOCKER_IMAGE}:latest
        '''
      }
    }

    stage('Push') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DH_USER', passwordVariable: 'DH_PASS')]) {
          sh '''
            echo "$DH_PASS" | docker login -u "$DH_USER" --password-stdin
            docker push ${DOCKER_IMAGE}:${BUILD_NUMBER}
            docker push ${DOCKER_IMAGE}:latest
          '''
        }
      }
    }

    stage('Deploy') {
      steps {
        sh '''
          kubectl apply -f deploymentsvc.yaml
          kubectl get deploy,po,svc
        '''
      }
    }
  }
}
