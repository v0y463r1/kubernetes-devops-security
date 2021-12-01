pipeline {
  agent any

  stages {
      stage('Build Artifact') {
            steps {
              sh "mvn clean package -DskipTests=true"
              archive 'target/*.jar' // teset
            }
        
      
      stage('unit test') {
            steps {
              sh "mvn test"
              
            }
        }  
    }
}
