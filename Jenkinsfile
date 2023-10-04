pipeline {
  agent any

  stages {
      stage('Build Artifact') {
            steps {
              sh "mvn clean package -DskipTests=true"
              archive 'target/*.jar'
            }
        }
      stage('Unit Tests - Junit and Jacoco'){
            steps {
              sh "mvn test"
            } 
            post{
              always {
                junit 'target/surefire-reports/*.xml'
                jacoco execPattern: 'target/jacoco.exec'
              }
            }
      }

      stage('Docker build and Push') {
        steps {
          script {
            docker.withRegistry('', 'docker-hub') {
              sh 'printenv'
              sh 'docker build -t andryonlinknet/practicemakeperfect:""$GIT_COMMIT"" .'
              sh 'docker push andryonlinknet/practicemakeperfect:""$GIT_COMMIT""'
      }
      }
      }
      }

      stage('Kubernetes Deployment - DEV') {
        steps {
          withKubeConfig([credentialsId: kubeconfig]) {
            sh "sed -i 's#replace#andryonlinknet/practicemakeperfect:{GIT_COMMIT}#g' k8s_deployment_service.yaml"
            sh "kubectl apply -f k8s_deployment_service.yaml"
          }
        }
      }
    
    }
}