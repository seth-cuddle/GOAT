pipeline {
    agent any
    stages {
	stage('Pull project code'){
		steps {
			echo 'downloading git directory..'
			git 'https://github.com/ibmappsec/GOAT.git'
		}
	}
	stage('Check git history'){
		steps{
			echo 'running talisman to check project history for secrets'
	//		sh '~/.talisman/bin/talisman_linux_amd64 --scan'
		}
	}   
	stage('Prepare SCA tool'){
		steps {
			script{				
				def exists = fileExists 'dependency-check-5.2.1-release.zip'
				if(exists){
					echo 'already exists'
				}else{
					// sh 'wget https://dl.bintray.com/jeremy-long/owasp/dependency-check-5.2.1-release.zip'
					//  sh 'unzip dependency-check-5.2.1-release.zip'	
					dependencyCheck additionalArguments: ''' --scan ./ --format HTML ''', odcInstallation: 'DP-Check'
				}
			}
		}
	}
	    stage('SonarQube Analsyis') {
      tools{
      jdk 'jdk17'
    }
      environment {
            scannerHome = tool 'sonar' // the name you have given the Sonar Scanner (Global Tool Configuration)
        }
                steps {
                    withSonarQubeEnv('sonar') {
                        sh ''' $scannerHome/bin/sonar-scanner -Dsonar.projectName=goat -Dsonar.url=http://172.28.208.1:9000/ \
                        -Dsonar.login=sqp_b2261d6bb9e293ec434456cbf0e9ffa107d6c81a  -Dsonar.projectKey=goat -Dsonar.java.binaries=. '''
                    }
                }
            }
    
	stage('Package project') {
            steps {
		    script{
			echo 'Packing maven project'
			    sh 'mvn install'
		    }
            }
        }
	stage('Run dependency check'){
		steps {
			echo 'running dependency check on java jar file'
			//fail on CVS score over 7
//			sh './dependency-check/bin/dependency-check.sh --project Testing --out m25.html --scan *.jar -f HTML --failOnCVSS 7'
//			sh './dependency-check/bin/dependency-check.sh --project Testing --out webgoat.html --scan ./webgoat-server/target/*.jar.* -f HTML --failOnCVSS 7'
//			sh './dependency-check/bin/dependency-check.sh --project Testing --out webwolf.html --scan ./webwolf/target/*.jar.* -f HTML --failOnCVSS 7'
			//let it run all without fail-on-CVS condition to create reports
//			sh './dependency-check/bin/dependency-check.sh --project Testing --out m25.html --scan *.jar -f HTML'
			sh './dependency-check/bin/dependency-check.sh --project Testing --out webgoat.html --scan ./webgoat-server/target/*.jar -f HTML'
			sh './dependency-check/bin/dependency-check.sh --project Testing --out webwolf.html --scan ./webwolf/target/*.jar -f HTML'

		}
	}
	}    
	

    post {
        always {
            echo 'This will always run'
        }
        success {
            echo 'This will run only if successful'
        }
        failure {
            echo 'This will run only if failed'
        }
        unstable {
            echo 'This will run only if the run was marked as unstable'
        }
        changed {
            echo 'This will run only if the state of the Pipeline has changed'
            echo 'For example, if the Pipeline was previously failing but is now successful'
        }
    }
}
