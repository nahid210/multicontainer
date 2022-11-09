pipeline {
    agent {
        label 'multi'
    }

    stages {
	
		stage('Git clone') {
            steps {
            	sh """
                   
                   https://github.com/nahid210/multicontainer
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

    }
}
