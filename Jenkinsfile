pipeline {
    agent any
    tools {
  maven 'M2_HOME'
}
    triggers {
  pollSCM('* * * * *')
    }
    stages {
        stage('maven package') {
            steps {
                sh 'mvn clean'
                sh 'mvn install'
                sh 'package'
            }
        }
          stage('test') {
            steps {
                echo 'test'
                sleep 5
            }
        }
          stage('deploy') {
            steps {
                echo 'deploy'
                sleep 4
            }
        }
    }
}pipeline {
    agent
}