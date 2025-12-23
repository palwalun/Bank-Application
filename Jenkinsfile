	pipeline{

	agent any
	 environment {
        SCANNER_HOME=tool 'SonarScanner'
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
		
		
	  }

	}