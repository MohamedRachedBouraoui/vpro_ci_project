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
}
        stages{
            stage('Build'){
                steps{
                    sh 'mvn -s settings.xml -DskipTests install'
                }
            }
        }
}