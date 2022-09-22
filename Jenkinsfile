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

    stage('docker') {
      steps {
        withDockerRegistry([credentialsId: "docker-hub", url: ""]) {
          sh "printenv"
          sh 'docker build -t an6eel/test:""$GIT_COMMIT"" .'
          sh 'docker push an6eel/test:""$GIT_COMMIT""'
        }
      }
    }   

    stage('Kubernetes deployment - dev') {
      steps {
        withKubeConfig([credentialsId: "kubeconfig"]) {
          sh "sed -i 's#replace#an6eel/test:${GIT_COMMIT}#g' k8s_deployment_service.yaml"
          sh "kubectl apply -f k8s_deployment_service.yaml"
        }
      }
    }

    stage('Mutation Test - PIT') {
      steps {
        sh "mvn org.pitest:pitest-maven:mutationCoverage"
      }
      post {
        always {
          pitmutation mutationStatsFile: '**/target/pit-reports/**/mutations.xml'
        }
      }
    }
  }
}