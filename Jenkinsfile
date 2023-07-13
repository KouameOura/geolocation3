pipeline{
    agent any
    tools {
        maven 'M2_HOME'
    }
    stages{
        stage('maven build'){
            steps{
                sh 'mvn clean install package'
            }
        }
        stage('upload to artifact'){
            steps{
                script{
                    def mavenPom = readMavenPom file: 'pom.xml'
                    nexusArtifactUploader artifacts: 
                    [[artifactId: "${POM_ARTIFACTID}",
                    classifier: '',
                    file: "target/${POM_ARTIFACTID}-${POM_VERSION}.${POM_PACKAGING}",
                    type: "${POM_PACKAGING}"]],
                    credentialsId: 'nexusIS',
                    groupId: "${POM_GROUPID}",
                    nexusUrl: '139.144.19.153:8081',
                    nexusVersion: 'nexus3',
                    protocol: 'http',
                    repository: 'biom',
                    version: "${POM_VERSION}"
                }
            }
        }
    }
}

