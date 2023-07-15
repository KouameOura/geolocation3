pipeline {
//     triggers {
//   pollSCM('* * * * *')
//    }
   agent {
    docker { image 'maven:3.8.6-openjdk-11-slim'}
   }
    tools {
  maven 'M2_HOME'
}

    stages {
        stage("build & SonarQube analysis") {  
            steps {
               withSonarQubeEnv('sonarQube') {
                   sh 'mvn verify org.sonarsource.scanner.maven:sonar-maven-plugin:sonar -Dsonar.projectKey=KouameOura_geolocation3 -X'
               }
            }
          }
        stage ('Check Quality Gate') {
            steps {
                echo 'Checking quality gate...'
                script {
                    timeout(time: 20, unit: 'MINUTES') {
                        def qg = waitForQualityGate()
                        if (qg.status != 'OK') {
                            error "Pipeline stopped because of quality gate status: ${qg.status}"
                    }
                }
            }

   }    }
               
        stage('maven package') {
            steps {
                sh 'mvn clean'
                sh 'mvn install -DskipTests'
                sh 'mvn package -DskipTests'
            }
        }

        stage ('deploy') {
            steps {
                echo 'deployment'
            }
        }  	    
    }
}