pipeline {
  agent any

  stages {
      stage('Build Artifact - Maven') {
            steps {
              sh "mvn clean package -DskipTests=true"
              archive 'target/*.jar' // test
            }
        
      }
      stage('Unit Tests - JUnit and Jacoco') {
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

        stage('SonarQube - SAST') {
      steps {
        withSonarQubeEnv('SonarQube') {
          //sh "mvn sonar:sonar -Dsonar.projectKey=numeric-application -Dsonar.host.url=http://devsecops-demo123.eastus.cloudapp.azure.com:9000"
          sh "mvn sonar:sonar -Dsonar.projectKey=numeric-application -Dsonar.host.url=http://devsecops-demo123.eastus.cloudapp.azure.com:9000 -Dsonar.login=af1c182f7d4621133daa7cd16a044ef24448bf9c"
        }
        timeout(time: 1, unit: 'MINUTES') {
          script {
            waitForQualityGate abortPipeline: true
          }
        }
      }
    }

        stage('Vulnerability Scan - Docker ') {
      steps {
        sh "mvn -X dependency-check:check"
      }
  //    post {
   //     always {
    //      dependencyCheckPublisher pattern: 'target/dependency-check-report.xml'
     //   }
     // }
    }


        stage('Docker Build and Push') {
          steps {
            withDockerRegistry([credentialsId: "docker-hub", url: ""]) {
              sh 'printenv'
              sh 'docker build -t v0y463r1/numeric-app:""$GIT_COMMIT"" .'
              sh 'docker push v0y463r1/numeric-app:""$GIT_COMMIT""'
            }
          }
        }
       stage('Kubernetes Deployment - DEV') {
          steps {
            withKubeConfig([credentialsId: 'kubeconfig']) {
              sh "sed -i 's#replace#v0y463r1/numeric-app:${GIT_COMMIT}#g' k8s_deployment_service.yaml"
              sh "kubectl apply -f k8s_deployment_service.yaml"
            }
          }
       }
    }
}
