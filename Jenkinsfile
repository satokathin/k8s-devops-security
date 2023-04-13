pipeline {
  agent any

  stages {
      stage('Build Artifact') {
            steps {
              sh "mvn clean package -DskipTests=true"
              archiveArtifacts 'target/*.jar' //so that they can be downloaded later
            }
        }   
    
      stage('Unit Tests') {
            steps {
              sh "mvn test"
            }
            post {
              alwasys {
                  jacoco(
                      execPattern: '**/build/jacoco/*.exec',
                      classPattern: '**/build/classes/java/main',
                      sourcePattern: '**/src/main'
                  ),
                    junit 'target/surefire-reports/*.xml',
              }
        }   
    }
}
