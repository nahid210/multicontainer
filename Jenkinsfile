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
                
                git clone https://github.com/nahid210/multicontainer
            	"""
            }
        }
	stage ('container stop and remove') {
            steps {
                sh 'docker stop $(docker ps -a -q)'
                sh 'docker rm $(docker ps -a -q)'
            }
        }
        stage('Docker container creation') {
            steps { 
                sh """
            	   sudo docker compose up -d
		   docker ps
                """
			}   
		}
        stage ('Unit Test') {
            steps {
		catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                sh 'docker exec  glue-backend ls -la'
                sh 'docker exec  glue-backend php artisan test'
                sh 'docker exec  glue-backend php artisan test --testsuite=Unit --stop-on-failure'
		sh "exit 1"
		}
            }
        }
        stage ('Larastan Test') {
            steps {
                sh 'docker exec glue-backend vendor/bin/phpstan analyse bootstrap'
                sh 'docker exec glue-backend vendor/bin/phpstan analyse public'
                sh 'docker exec glue-backend vendor/bin/phpstan analyse stubs'
            }
        }     
	stage ('Codeception Test') {
            steps {
                sh 'docker exec glue-backend composer require --dev codeception/codeception'
                sh 'docker exec glue-backend composer require codeception/module-phpbrowser --dev'
                sh 'docker exec glue-backend composer require codeception/module-asserts --dev'
            }
        }	   
    }
}
