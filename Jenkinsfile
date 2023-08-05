def COLOR_MAP = [
    'SUCCESS':'good',
    'FAILURE':'danger',
]

pipeline{

        agent any
        tools{
            maven "MAVEN3"
            jdk "OracleJDK8"
        }

environment{
    NEXUS_USER = 'admin'
    NEXUS_PASS = 'admin'
    SNAP_REPO = 'vprofile-snapshot'
    RELEASE_REPO = 'vprofile-release'
    NEXUS_GRP_REPO = 'vprofile-group'
    CENTRAL_REPO = 'vpro-maven-central'
    NEXUSIP = '172.31.83.23'
    NEXUSPORT = '8081'
    NEXUS_LOGIN = 'nexuslogin'
    SONARSERVER = 'sonarserver'
    SONARSCANNER = 'sonarscanner'
}
        stages{
            stage('Build'){
                steps{
                    sh 'mvnz -s settings.xml -DskipTests install'
                }
                post{
                    success {
                        echo "now archiving 💱💱💱"
                        archiveArtifacts artifacts: '**/*.war'
                    }
                }
            }
            stage('Test'){
                steps{
                    sh 'mvn -s settings.xml test'
                }
            }

            stage('Checkstyle analysis'){
                steps{
                    sh 'mvn -s settings.xml checkstyle:checkstyle'
                }
            }

             stage('Sonar Analysis') {
                environment {
                    scannerHome = tool "${SONARSCANNER}"
                }
                steps {
                    withSonarQubeEnv("${SONARSERVER}") {
                        sh '''${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=vprofile \
                        -Dsonar.projectName=vprofile \
                        -Dsonar.projectVersion=1.0 \
                        -Dsonar.sources=src/ \
                        -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
                        -Dsonar.junit.reportsPath=target/surefire-reports/ \
                        -Dsonar.jacoco.reportsPath=target/jacoco.exec \
                        -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml'''
                    }
                }
            }
             stage('Quality gate'){
                steps{
                    timeout(time: 1,unit: 'HOURS'){
                        //Indicates whether to set the pipeline to UNSTAIBLE if set to "true"
                        waitForQualityGate abortPipeline: true
                    }
                }
            }

            stage("Upload artifact"){
                steps{
                        nexusArtifactUploader(
                        nexusVersion: 'nexus3',
                        protocol: 'http',
                        nexusUrl: "${NEXUSIP}:${NEXUSPORT}",
                        groupId: 'QA',
                        version: "${env.BUILD_ID}-${env.BUILD_TIMESTAMP}",
                        repository: "${RELEASE_REPO}",
                        credentialsId: "${NEXUS_LOGIN}",
                        artifacts: [
                            [artifactId: 'vproapp',
                            classifier: '',
                            file: 'target/vprofile-v2.war',
                            type: 'war']
                        ]
                    )
                }   
            }

        }
        post{
            always {
                echo 'Slack notifications 📤📤📤'
                slackSend channel: '#jenkinscicd',
                color: COLOR_MAP[currentBuild.currentResult],
                message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} \n More info at: ${env.BUILD_URL}"
            }
        }

}