pipeline {
    agent {label "ip-172-26-14-37" } 
    environment {
        NEXUS_VERSION = "nexus3"
        NEXUS_PROTOCOL = "http"
        NEXUS_URL = "http://44.204.223.239:8081//repository/nexus-maven-repo/"
        NEXUS_REPOSITORY = "nexus-maven-repo"
        NEXUS_CREDENTIAL_ID = "nexusCredentials"
        HOME_DIR = "/home/application/workspace"
    }
    stages {
        
        stage ('BitBucket Integration') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/Dev_Sec_Ops_Team_1']], extensions: [], userRemoteConfigs: [[credentialsId: 'bitbucket', url: 'https://bitbucket.org/appincubators/glue/src/Dev_Sec_Ops_Team_1']]])
            }
        }  
        stage ('container stop and remove') {
            steps {
                sh 'docker stop $(docker ps -a -q)'
                sh 'docker rm $(docker ps -a -q)'
            }
        }    
        stage ('docker build') {
            steps {
                sh 'cd /home/application/workspace/GlueProject/Docker/glue_in_multicontainer && docker-compose -f docker-compose.yml up -d'
                sh 'docker ps'
            }
        }
}
}
