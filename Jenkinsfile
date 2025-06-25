pipeline {
  agent any
  stages {
    stage('Clone') {
      steps {
        git url: 'https://github.com/AbhishekHibare/spring-petclinic.git'
      }
    }
    stage('Build') {
      steps {
        sh 'mvn clean package'
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
  }
  post {
    success {
      echo 'Build succeeded!'
    }
    failure {
      echo 'Build failed!'
    }
    always {
      cleanWs()
    }
  }
}
