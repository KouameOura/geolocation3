pipeline {
    triggers {
  pollSCM('* * * * *')
    }
   agent {
    docker { image 'maven:3.8.6-openjdk-11-slim'}
   }
    tools {
  maven 'M2_HOME'
}

    stages {
        stage("build & SonarQube analysis") {  
            agent any
            steps {
               withSonarQubeEnv('sonarQube') {
                   sh 'mvn sonar:sonar'
               }
            }
          }
               
        stage('maven package') {
            steps {
                sh 'mvn clean'
                sh 'mvn install'
                sh 'mvn package'
            }
        }   	    
    }
}
