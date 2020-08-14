pipeline {
	agent any
	tools {
		jdk 'JAVA_HOME'
		maven 'M2_HOME'
	}
	stages {
		stage("Maven-Build"){
			input{
message "Press Ok to continue"
submitter "user1,user2"
parameters {
string(name:'username', defaultValue: 'user', description: 'Username of the user pressing Ok')
}
			}
			steps {
				
				sh 'mvn -Dmaven.test.skip=true install'
			}
			
		}
		stage ("Docker-Login") {
			steps {
				
				sh 'aws ecr create-repository --repository-name $JOB_NAME --region eu-west-1'
				sh 'aws ecr get-login-password --region eu-west-1 | docker login --username AWS --password-stdin 936422292136.dkr.ecr.eu-west-1.amazonaws.com'
			}
		}
		stage ("Deleting-Previous-Docker-Builds"){
			steps {
				
				sh 'docker rmi -f "$(docker images -aq)"'
			}
		}
		stage ("Docker-Build-Services") {
			steps {
				
				sh "docker build -t search:0.${env.BUILD_ID} search/"
				
				sh "docker build -t website:0.${env.BUILD_ID} website/"
			}
		}
		stage ("Tagging-docker-images") {
			steps {
				
				sh "docker tag search:0.${env.BUILD_ID} kiran437/search:0.${env.BUILD_ID}"
				
				sh "docker tag website:0.${env.BUILD_ID} kiran437/website:0.${env.BUILD_ID}"
			}
		}
		stage ("Pushing-Images-to-Registry"){
			steps {
				
				sh "docker push kiran437/search:0.${env.BUILD_ID}"
				
				sh "docker push kiran437/website:0.${env.BUILD_ID}"
			}
		}
		stage('DeployToProduction') {
            		steps {
                		kubernetesDeploy(
                    			kubeconfigId: 'kubeconfig',
                    			configs: 'flight-booking-kube.yml'
                		)
            		}
        	}
	}
}
