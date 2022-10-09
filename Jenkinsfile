pipeline {
    agent { label 'JDK11' }
    options { 
        timeout(time: 1, unit: 'HOURS')
    }
    triggers {
        cron('0 * * * *')
    }
    stages {
        stage('Source Code') {
            steps {
                git url: 'https://github.com/khaleel2788/my-spring-petclinic.git', 
                branch: 'sprint_release_1'
            }
        }    
        stage('Artifactory-Configuration') {
            steps {
                rtMavenDeployer (
                    id: 'spc-deployer',
                    serverId: 'JFROG_INSTANCE',
                    releaseRepo: 'kpjfrog-libs-release-local',
                    snapshotRepo: 'kpjfrog-libs-snapshot-local',
                )
            }
        }

        stage('Build the Code and sonarqube-analysis') {
            steps {
                // withSonarQubeEnv('SONAR_LATEST') {
                //     sh script: "mvn ${params.GOAL} sonar:sonar"
                // }
                rtMavenRun (
                    // Tool name from Jenkins configuration.
                    tool: 'MVN_DEFAULT',
                    pom: 'pom.xml',
                    goals: 'clean install',
                    // Maven options.
                    deployerId: 'spc-deployer',
                )

            }
        }
        stage('reporting') {
            steps {
                junit testResults: 'target/surefire-reports/*.xml'
            }
        }
            // stage("Quality Gate") {
            //     steps {
            //         timeout(time: 1, unit: 'HOURS') {
            //             waitForQualityGate abortPipeline: true
            //         }

            //     }
            // }
    }
}
