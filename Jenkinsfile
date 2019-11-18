pipeline {
   agent any

   tools {
      // Install the Maven version configured as "M3" and add it to the path.
      maven "maven"
   }

    // environment {
    // // This can be nexus3 or nexus2
    //NEXUS_VERSION = "nexus3"
    // // This can be http or https
    //NEXUS_PROTOCOL = "http"
    // // Where your Nexus is running
    //NEXUS_URL = "localhost:8081"
    // // Repository where we will upload the artifact
    //NEXUS_REPOSITORY = "nexus-repository"
    // // Jenkins credential id to authenticate to Nexus OSS
    //NEXUS_CREDENTIAL_ID = "nexus-credentials"
    // }

   stages {
        stage('CHECKOUT') {
            steps {
                git 'https://github.com/allainmoyo/spring-boot.git'
            }
        }
        stage('BUILD') {
            steps {
                sh "mvn clean install -f ./spring-boot-tests/spring-boot-smoke-tests/spring-boot-smoke-test-web-ui/pom.xml"
            }
        }


        //  post {
        //     // If Maven was able to run the tests, even if some of the test
        //     // failed, record the test results and archive the jar file.
        //     success {
        //       archiveArtifacts 'spring-boot-tests/spring-boot-smoke-tests/spring-boot-smoke-test-web-ui/target/*.jar'
        //         }
        //     }


        stage("UPLOAD ARTIFACT") {
            steps {
                nexusArtifactUploader(
                    nexusVersion: 'nexus3',
                    protocol: 'http',
                    nexusUrl: '172.17.0.2:8081',
                    groupId: 'java',
                    version: 'build-${BUILD_NUMBER}',
                    repository: 'nexus-repository',
                    credentialsId: 'nexus-credentials',
                    artifacts: [
                        [artifactId: 'spring-boot-smoke-test-web-ui',
                         classifier: '',
                         file: '/var/jenkins_home/workspace/build-deploy_master/spring-boot-tests/spring-boot-smoke-tests/spring-boot-smoke-test-web-ui/target/spring-boot-smoke-test-web-ui-2.2.1.BUILD-SNAPSHOT.jar',
                         type: 'jar']
                    ]
                )
            }
        }
      //stage("UPLOAD ARTIFACT") {
      //      steps {
      //          nexusArtifactUploader(
      //             nexusVersion: 'nexus3',
      //              protocol: 'http',
      //              nexusUrl: 'localhost:8081',
      //              groupId: 'java',
      //              version: 'build-${BUILD_NUMBER}',
      //              repository: 'nexus-repository',
      //              credentialsId: 'nexus-credentials',
      //              artifacts: [
      //                  [artifactId: 'spring-boot-smoke-test-web-ui',
      //                   classifier: '',
      //                   file: './spring-boot-master/spring-boot-tests/spring-boot-smoke-tests/spring-boot-smoke-test-web-ui/target/spring-boot-smoke-test-web-ui-2.2.1.BUILD-SNAPSHOT.jar',
      //                   type: 'jar']
      //              ]
      //          )
      //      }
      //  }
    }
}
