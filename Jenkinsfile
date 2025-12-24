	pipeline{

	agent any
	 environment {
        SCANNER_HOME=tool 'SonarScanner'
		ACR_LOGIN_SERVER = "devopsproject1.azurecr.io"
		IMAGE_NAME = 'bankapp'
		TAG = 'latest'
    }
	  stages{
		stage('Checkout'){
		 steps{
		  checkout scm	
		 }
		}
		stage('Build'){
		 steps{
		  sh 'mvn clean package -DskipTests'	
		 }
		}
		stage("Sonarqube Analysis "){
            steps{
                withSonarQubeEnv('SonarQube') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=BankApp \
                    -Dsonar.java.binaries=. \
                    -Dsonar.projectKey=BankApp '''
    
                }
            }
        }
		stage('OWASP Dependency-Check') {
          steps {
           dependencyCheck additionalArguments: '--scan pom.xml', odcInstallation: 'Dependency-Check'
              dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
       }
		stage('Build Docker Image') {
          steps {
            sh 'docker build -t bankapp:latest .'
            }
       }
		stage('Login to ACR') {
       steps {
         withCredentials([usernamePassword(
             credentialsId: 'acr-creds',
             usernameVariable: 'ACR_USER',
             passwordVariable: 'ACR_PASS'
         )]) {
             sh '''
               echo $ACR_PASS | docker login $ACR_LOGIN_SERVER \
               -u $ACR_USER --password-stdin
             '''
           }
          }
         }
	    stage('Tag Image') {
        steps {
         sh '''
           docker tag ${IMAGE_NAME}:${TAG} \
           $ACR_LOGIN_SERVER/${IMAGE_NAME}:${TAG}
         '''
          }
        }
	  }

	}