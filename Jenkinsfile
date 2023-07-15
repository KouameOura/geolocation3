pipeline {
//     triggers {
//   pollSCM('* * * * *')
//    }
   agent any
    tools {
  maven 'M2_HOME'
}
environment {
    registry = '251095668064.dkr.ecr.us-east-1.amazonaws.com/jenkins'
    registryCredential = 'aws_ecr_id'
    dockerimage = ''
}

    stages {
        stage("build & SonarQube analysis") {  
            steps {
               withSonarQubeEnv('sonarQube') {
                   sh 'mvn sonar:sonar -Dsonar.projectKey=KouameOura_geolocation3 -Dsonar.java.binaries=.'
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

            } 
        }    

               
        stage('maven package') {
            steps {
                sh 'mvn clean'
                sh 'mvn install -DskipTests'
                sh 'mvn package -DskipTests'
            }
        }

        stage('Build Image') {
            agent {
                docker { image 'maven:3.8.6-openjdk-11-slim'}
            }
            steps {
                script{
                  def mavenPom = readMavenPom file: 'pom.xml'
                  POM_VERSION = "${mavenPom.version}"
                  echo "${POM_VERSION}"
                  dockerImage = docker.build registry + ":${POM_VERSION}"
                } 
            }
        }
        stage('push image') {
            agent any
            steps{
                script{ 
                    docker.withRegistry("https://"+registry,"ecr:us-east-1:"+registryCredential) {
                        dockerImage.push()
                    }
                }
            }
        } 	    
    }
}