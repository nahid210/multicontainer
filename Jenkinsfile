pipeline {
    agent {
        label 'glueship'
    }

    stages {
	
		stage('Git clone') {
            steps {
            	sh """
                   rm -fr glueship
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

    post {
        success {
            script {
				sh """
					echo "This is success"
				"""

            }
        }
        failure {
            script {
				sh """
					echo "This is failed"
				"""
            }
        }

    }
}
