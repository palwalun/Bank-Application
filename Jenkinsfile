	pipeline{

	agent any
	 environment {
        SCANNER_HOME=tool 'SonarScanner'
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
		
	  }

	}