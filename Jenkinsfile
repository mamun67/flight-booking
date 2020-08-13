pipeline {
	agent any
	tools {
		jdk 'JAVA_HOME'
		maven 'M2_HOME'
	}
	stages {
		stage("Maven-Build"){
			steps {
				
				sh 'mvn -Dmaven.test.skip=true install'
			}
			script {
                          timeout(time: 10, unit: 'MINUTES') {
                              input(id: "Deploy Gate", message: "Deploy environment?", ok: 'Deploy')
                              }
                         }

		}
		stage ("Docker-Login") {
			steps {
				
				sh 'docker login -u kiran437 -p Kiran@123'
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
