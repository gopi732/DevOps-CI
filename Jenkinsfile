// Declarative pipeline
pipeline {
    agent any
    stages {
        stage ('Clean Reports') {
            steps {
                sh 'rm -rf test-reports || true'  
            }
        }
	stage ('Unit test') {
	    steps {
		sh 'python3 test.py && coverage run test.py && coverage xml && coverage report app.py test.py'
	    }
	}	
	stage('SonarQube Analysis') {
	    environment {
		scannerHome = tool 'SonarQube Scanner'
            }
	    steps {				
		withSonarQubeEnv('Sonar') {
		     sh '${scannerHome}/bin/sonar-scanner \
 	   	     -D sonar.projectKey=DevOps \
		     -D sonar.python.coverage.reportPaths=coverage.xml'	
		}
	    }
	}
        stage ('Generate Test Reports') {
            steps {
                junit 'test-reports/*.xml'
            }
        }
	stage ('Publish Artifactory') {
	    steps {
		    withCredentials([usernamePassword(credentialsId: 'artifactory', passwordVariable: 'passwd', usernameVariable: 'user')]) {
    		         sh 'jf rt upload test-reports/ DevOps-CI/'
		    }	
	    }
	}
    }
}
