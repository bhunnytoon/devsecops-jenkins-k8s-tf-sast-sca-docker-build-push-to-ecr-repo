pipeline {
  agent any
  tools { 
        maven 'Maven_3_2_5'  
    }
   stages{
    stage('Compile and SAST Scanning') {
            steps {	
		sh 'mvn clean verify sonar:sonar -Dsonar.projectKey=bhunnytoon -Dsonar.organization=bhunnytoon -Dsonar.host.url=https://sonarcloud.io -Dsonar.login=f478dceb39e2fb62ce2622216a3112a7186cb2e1'
			}
    }

	stage('SCA Scanning') {
            steps {		
				withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
					sh 'mvn snyk:test -fn'
				}
			}
    }

	stage('Build Image') { 
            steps { 
               withDockerRegistry([credentialsId: "dockerlogin", url: ""]) {
                 script{
                 app =  docker.build("asg")
                 }
               }
            }
    }

	stage('Push Image') {
            steps {
                script{
                    docker.withRegistry('https://211125471600.dkr.ecr.ap-southeast-1.amazonaws.com/asg', 'ecr:ap-southeast-1:aws-credentials') {
                    app.push("latest")
                    }
                }
	    }
    }    
  }
}
