pipeline {
  agent any
  
  environment {
      DOCKERHUB_CREDENTIALS = credentials('dockerhub')
  }

  stages {
      stage('Build Artifact') {
            steps {
              sh "mvn clean package -DskipTests=true"
              archiveArtifacts 'target/*.jar' //so that they can be downloaded later
            }
        }   
    
      stage('Unit Tests - JUnit and Jacoco') {
            steps {
              sh "mvn test"
            }
            post {
              always {
                junit 'target/surefire-reports/*.xml'
                jacoco(
                      execPattern: 'target/jacoco.exec'
                  )
                }
              }
        }
		
	stage('Docker') {
            steps {
              sh "docker build -t devsato/numeric-app:${env.BUILD_ID} ."
	      sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
	      sh "docker push devsato/numeric-app:${env.BUILD_ID}"
            }
		post {
			always {
				sh 'docker logout'
			}
		}
        }
    }
}
