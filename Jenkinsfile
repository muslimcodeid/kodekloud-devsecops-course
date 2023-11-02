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
      stage('Mutation Test - PIT'){
            steps {
              sh "mvn org.pitest:pitest-maven:mutationCoverage"
            } 
            post{
              always {
                pitmutation mutationStatsFile: '**/target/pit-reports/**/mutations.xml'
              }
            }
          }

      stage('Sonarqube - SAST') {
        steps {
             sh "mvn clean verify sonar:sonar -Dsonar.projectKey=numeric-application -Dsonar.host.url:http://srv.standbywith.me:8080 -Dsonar.login=sqp_a143d4b26a6758e4489ee8ee5ef7420e95d9b972"
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
          withKubeConfig([credentialsId: 'kubeconfig']) {
            sh "sed -i 's#replace#andryonlinknet/practicemakeperfect:${GIT_COMMIT}#g' k8s_deployment_service.yaml"
            sh "kubectl apply -f k8s_deployment_service.yaml"
          }
        }
      }
    
    }
}