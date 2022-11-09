pipeline {
    agent {
        label 'glueship'
    }

    stages {
	
		stage('Git clone') {
            steps {
            	sh """
                   rm -fr glueship
                   git clone https://github.com/nahid210/glueship
            	"""

            }
        }
        stage('Docker image Build') {
            steps {
            	sh """
                    docker build -t nginxtest:v1.00 .
            	"""
            }
        }

        stage('Docker container creation') {
            steps { 
                sh """
            	   docker run -d --name glueshiptest --restart always -p 8088:80 nginxtest:v1.00 
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
