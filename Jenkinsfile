pipeline {
  agent any
  tools { 
        maven 'Maven_3_6_3'  
    }
   stages{
    stage('CompileandRunSonarAnalysis') {
            steps {	
		sh 'mvn clean verify sonar:sonar -Dsonar.projectKey=dubcygoat -Dsonar.organization=dubcygoat -Dsonar.host.url=https://sonarcloud.io -Dsonar.token=44a88fd48f3b572c9a2ca39da30ceef635d992ee'
			}
    }

	stage('RunSCAAnalysisUsingSnyk') {
            steps {		
				withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
					sh 'mvn snyk:test -fn'
				}
			}
    }

	stage('Build') { 
            steps { 
               withDockerRegistry([credentialsId: "dockerlogin", url: ""]) {
                 script{
                 app =  docker.build("goat")
                 }
               }
            }
    }

	stage('Push') {
            steps {
                script{
                    docker.withRegistry('https://058264531143.dkr.ecr.us-west-2.amazonaws.com', 'ecr:us-west-2:aws-credentials') {
                    app.push("latest")
                    }
                }
            }
    	}
	    
	   
	stage('Kubernetes Deployment of dubcy Bugg Web Application') {
	   steps {
	      withKubeConfig([credentialsId: 'kubelogin']) {
		  sh('kubectl delete all --all -n devsecops')
		  sh ('kubectl apply -f deployment.yaml --namespace=devsecops')
		}
	      }
   	}

  }
}
