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
          docker.withRegistry(['https://registry.hub.docker.com', 'cc47a082-cdaa-4eff-9447-73ffcf4728c1']) {
            sh 'printenv'
            sh 'docker build -t andryonlinknet/practicemakeperfect:""$GIT_COMMIT"" .'
            sh 'docker push andryonlinknet/practicemakeperfect:""$GIT_COMMIT""'
      }
      }
      }

    
    }
}