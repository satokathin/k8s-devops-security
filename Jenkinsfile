pipeline {
  agent any

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
                      execPattern: '**/build/jacoco/*.exec',
                      classPattern: '**/build/classes/java/main',
                      sourcePattern: '**/src/main'
                  )
                }
              }
        }
    }
}
