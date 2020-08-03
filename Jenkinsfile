pipeline {
   // execute on any worker node
   agent any

   // execute on specific worker:
   // agent label 'worker'

   options {
      // clean old builds
      buildDiscarder(logRotator(artifactDaysToKeepStr: '5', daysToKeepStr: '5', numToKeepStr: '3', artifactNumToKeepStr: '3'))

      // timeout for job building
      timeout(time: 30, unit: 'MINUTES')

      // colorise output plugin
      ansiColor('xterm')

      // print timestamps
      timestamps()
   }

   tools {
      // Install the Maven version configured as "maven" and add it to the path.
      maven "maven"
   }

   stages {
        //get new code from repository
        stage("*** CHECKOUT ***") {
            steps {
                sh label: '*** CLONING REPOSITORY ***', script: """ """
                git 'https://github.com/aurcame/spring-boot.git'
            }
        }

         //building the code to get new artifact
        stage("*** BUILD ***") {
            steps {
                sh label: '*** BUILDING ARTIFACT WITH MAVEN ***', script: """
                    mvn clean install -f "./spring-boot-tests/spring-boot-smoke-tests/spring-boot-smoke-test-web-ui/pom.xml"
                    # save build number to file to check it in QA/CI deployment jobs as last
                    echo ${BUILD_NUMBER} > ~/build.number
                """
            }
        }

        // upload artifact to the Nexus3 repository
        stage("*** UPLOAD ARTIFACT ***") {
            steps {
                sh label: '*** UPLOADING ARTIFACT TO NEXUS ***', script: """ """
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

        // deployment to QA and CI instances with separate freestyle jobs
        stage ("*** DEPLOY ***") {
           steps { 
            sh label: '*** CONTINOUS DEPLOYMENT to CI host ***', script: """ """
            // automaticaly deploy to CI host: Continous Deployment
            build job: 'GW/deploy_jar_CI'          

            // should manually deploy to QA: Continous Delivery. Will be started in eploy_jar_QA job
            // build job: 'GW/deploy_jar_QA'
           }
        }
    }
}
