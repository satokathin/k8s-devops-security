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
		
	stage('Docker build and push') {
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
	  
	 stage('K8 NumericApp deployment') {
            steps {
                withKubeConfig([credentialsId: 'kubeconfig']) {
			sh "sed -i s#replace#devsato/numeric-app:${env.BUILD_ID}#g k8s_deployment_service.yaml"
                	sh "kubectl apply -f k8s_deployment_service.yaml"
                }
            }
        }
	  
	stage('K8 nodeService Deployment') {
            steps {
                withKubeConfig([credentialsId: 'kubeconfig']) {
			sh 'kubectl create deploy node-app --image siddharth67/node-service:v1'
                	sh "kubectl expose deploy node-app --name node-service --port 5000 --type ClusterIP"
                }
            }
        }
    }
}
