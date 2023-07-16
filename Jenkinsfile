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

    NEXUS_VERSION = "nexus3"
    NEXUS_PROTOCOL = "http"
    NEXUS_URL = "139.144.19.153:8081"
    NEXUS_REPOSITORY = "biom"
    NEXUS_CREDENTIAL_ID = "NexusIS"
    POM_VERSION = ''
}

    stages {
        stage("build & SonarQube analysis") {  
            agent {
               docker { image 'maven:3.8.6-openjdk-11-slim'}
         }
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
           
            steps{
                script{ 
                    docker.withRegistry("https://"+registry,"ecr:us-east-1:"+registryCredential) {
                        dockerImage.push()
                    }
                }
            }
        } 
         // Project Helm Chart push as tgz file
        stage("pushing the Backend helm charts to nexus"){
            steps{
                script{
                    withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId:'nexusIS', usernameVariable: 'jenkins-user', passwordVariable: 'docker_pass']]) {
                            def mavenPom = readMavenPom file: 'pom.xml'
                            POM_VERSION = "${mavenPom.version}"
                            sh "echo ${POM_VERSION}"
                            sh "tar -czvf  app-${POM_VERSION}.tgz app/"
                            sh "curl -u jenkins-user:$docker_pass http://139.144.19.153:8081/repository/biom-helm --upload-file app-${POM_VERSION}.tgz -v"  
                    }
                } 
            }
        }     		    
    }
}