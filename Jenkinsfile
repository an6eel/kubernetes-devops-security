pipeline {
  agent any

  stages {
    stage('Build Artifact') {
      steps {
        sh "mvn clean package -DskipTests=true"
        archive 'target/*.jar' 
      }
    }

    stage('Unit tests') {
      steps {
        sh "mvn test"
      }
      post {
        always {
          junit 'target/surefire-reports/*.xml'
          jacoco execPattern: 'target/jacoco.exec'
        }
      }
    }

    stage('Unit tests') {
      steps {
        sh "printenv"
        sh 'docker build an6eel/test:""$GIT_COMMIT"" .'
        sh 'docker push an6eel/test:""$GIT_COMMIT""'
      }
    }   
  }
}