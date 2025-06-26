pipeline {
  agent any

  environment {
    HARBOR_URL = '10.139.109.196:4424'
    HARBOR_PROJECT = 'testing'
    IMAGE_NAME = "${HARBOR_URL}/${HARBOR_PROJECT}/spring-petclinic"
    IMAGE_TAG = "build-${BUILD_NUMBER}"
  }

  stages {
    stage('Build') {
      steps {
        sh 'mvn clean package -DskipTests'
      }
    }

    stage('Test') {
      steps {
        sh 'mvn test'
      }
    }

    stage('Archive Artifact') {
      steps {
        archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
      }
    }

    stage('Docker Build') {
      steps {
        sh '''
        docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .
        '''
      }
    }

    stage('Docker Push') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'harbor-creds', usernameVariable: 'HARBOR_USER', passwordVariable: 'HARBOR_PASSWORD')]) {
          sh '''
          echo "$HARBOR_PASSWORD" | docker login https://${HARBOR_URL} -u "$HARBOR_USER" --password-stdin
          docker push ${IMAGE_NAME}:${IMAGE_TAG}
          docker logout https://${HARBOR_URL}
          '''
        }
      }
    }
  }

  post {
    success {
      echo '✅ Build and push succeeded!'
    }
    failure {
      echo '❌ Build or push failed!'
    }
    always {
      cleanWs()
    }
  }
}
