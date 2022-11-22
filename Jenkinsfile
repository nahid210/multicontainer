pipeline {
    agent {label "app" } 
    environment {
        NEXUS_VERSION = "nexus3"
        NEXUS_PROTOCOL = "http"
        NEXUS_URL = "http://178.62.237.10:8081/repository/Glue-Artifact//"
        NEXUS_REPOSITORY = "Glue-Artifact"
        NEXUS_CREDENTIAL_ID = "nexusCredentials"
        HOME_DIR = "/home/devsecops1/workspace/"
    }
    stages {
        
        stage ('BitBucket Integration') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/Dev_Sec_Ops_Team_1']], extensions: [], userRemoteConfigs: [[credentialsId: 'devsecops', url: 'https://bitbucket.org/appincubators/glue/src/Dev_Sec_Ops_Team_1/']]])
            }
        }  
           stage ('Clean Artifacts') {
            steps {
                sh 'ls'
                sh 'rm -rf $HOME_DIR/artifact'
                sh 'rm -rf $HOME_DIR/Glueproject/artifact.tar.gz'
            }
        }     
        
          stage ('Docker Build') {
            steps {
                sh 'docker stop $(docker ps -a -q)'
                sh 'docker rm $(docker ps -a -q)'
                sh 'cd /home/devsecops1/workspace/Glueproject/Docker/glue_in_multicontainer && docker-compose -f docker-compose.yml up -d'
                sh 'docker ps' 
            }
        }
      stage ('Unit Test') {
            steps {
		catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                sh 'docker exec  glue-backend ls -la'
                sh 'docker exec  glue-backend php artisan make:test UserTest --unit'
                //sh 'docker exec  glue-backend php artisan test'
                sh 'docker exec  glue-backend php artisan test --testsuite=Unit --stop-on-failure'
		        //sh "exit 1"
		      }
            }
        }
        
          stage ('Larastan Test') {
            steps {
                //sh 'docker exec glue-backend vendor/bin/phpstan analyse bootstrap'
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
         stage ('Code Coverage Test') {
            steps {
				catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                sh 'docker exec glue-backend php artisan test --testsuite=Feature --stop-on-failure'
                sh 'docker exec glue-backend vendor/bin/phpunit --coverage-html reports/' 
                // sh 'docker exec glue-backend vendor/bin/phpunit --coverage-html reports/'
                //sh 'docker exec glue-backend php artisan test --coverage'
                //sh 'docker exec glue-backend php artisan test --coverage --min=80.3'
				sh "exit 1"
				}
            }
        }
        stage ('PHPCS Test') {
            steps {
				catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                sh 'docker exec glue-backend composer require --dev squizlabs/php_codesniffer'
                sh 'docker exec glue-backend vendor/bin/phpcs app database storage bootstrap'
                sh 'docker exec glue-backend vendor/bin/phpcbf app database storage bootstrap'
				sh "exit 1"
				}
            }
        }
             
          stage ('Build') {
            steps {
                sh 'cp -r $HOME_DIR/Glueproject  $HOME_DIR/artifact'
                sh 'tar -czvf $HOME_DIR/artifact.tar.gz  $HOME_DIR/artifact'
                sh 'mv $HOME_DIR/artifact.tar.gz $HOME_DIR/Glueproject'
                sh 'ls -la'
            }
        }
         stage ('Upload Artifact') {
            steps {
                sh 'curl -v -u ${nexus_user}:${nexus_pass} --upload-file artifact.tar.gz ${NEXUS_URL}'
            }
        }
        stage ('Acceptance Test') {
            steps {
				catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                //sh 'docker exec glue-backend vendor/bin/codecept generate:cest Acceptance bootstrap'
                sh 'docker exec glue-backend vendor/bin/codecept run' 
                // sh 'docker exec glue-backend vendor/bin/phpunit --coverage-html reports/'
                //sh 'docker exec glue-backend php artisan test --coverage'
                //sh 'docker exec glue-backend php artisan test --coverage --min=80.3'
				sh "exit 1"
				}
            }
        }
}
}
