pipeline {
   agent any
   stages {
        stage('CHECKOUT') {
            steps {
               ansiColor('xterm') {
                git 'https://github.com/allainmoyo/spring-boot.git'
               }
            }
        }
        stage('BUILD') {
            steps {
               ansiColor('xterm') {
                sh "mvn clean install -f ./spring-boot-tests/spring-boot-smoke-tests/spring-boot-smoke-test-web-ui/pom.xml"
               }
            }
        }
      
        stage("UPLOAD ARTIFACT") {
            steps {
               ansiColor('xterm') {
                nexusArtifactUploader(
                    nexusVersion: 'nexus3',
                    protocol: 'http',
                    nexusUrl: 'localhost:8081',
                    groupId: 'java',
                    version: 'build-${BUILD_NUMBER}',
                    repository: 'maven-repository',
                    credentialsId: 'nexus-credentials',
                    artifacts: [
                        [artifactId: 'spring-boot-smoke-test-web-ui',
                         classifier: '',
                         file: 'spring-boot-tests/spring-boot-smoke-tests/spring-boot-smoke-test-web-ui/target/spring-boot-smoke-test-web-ui-2.2.1.BUILD-SNAPSHOT.jar',
                         type: 'jar']
                    ]
                )
               }
            }
        }

        stage ('DEPLOY') {
            steps {
               ansiColor('xterm') {
    
                build job: 'deploy_jar_QA'
                build job: 'deploy_jar_CI'
               }
            }
        }
    }
}
