pipeline {
    agent {
        label 'multi'
    }
 environment {
        NEXUS_VERSION = "nexus3"
        NEXUS_PROTOCOL = "http"
        NEXUS_URL = "http://192.168.1.127:8081/repository/glue-app/"
        NEXUS_REPOSITORY = "glue-app"
        NEXUS_CREDENTIAL_ID = "nexus"
        HOME_DIR = "/opt/lampp/htdocs/workspace/"
      }
    stages {
	
		stage('Git clone') {
            steps {
            	sh """
                rm -fr multicontainer
                git clone https://github.com/nahid210/multicontainer
            	"""

            }
        }

        stage('Docker container creation') {
            steps { 
                sh """
            	   sudo docker compose up
		   
                """
    }
        }		    
         stage('Unit Test) {
            steps { 
                sh """
            	   php artisan make:test UserTest --unit
		   
                """
            }
        }

    }
}
