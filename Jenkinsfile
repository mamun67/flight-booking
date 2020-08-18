pipeline {
	agent any
	tools {
		jdk 'JAVA_HOME'
		maven 'M2_HOME'
	}
	stages {
		stage("Maven-Build"){
			script{
try {
    userInput = input(
        id: 'Proceed1', message: 'Was this successful?', parameters: [
        [$class: 'BooleanParameterDefinition', defaultValue: true, description: '', name: 'Please confirm you agree with this']
        ])
} catch(err) { // input false
    def user = err.getCauses()[0].getUser()
    userInput = false
    echo "Aborted by: [${user}]"
}
}
node {
    if (userInput == true) {
        // do something
        echo "this was successful"
    } else {
        // do something else
        echo "this was not successful"
        currentBuild.result = 'FAILURE'
    } 
}
			
			
			}
			steps {
				
				sh 'mvn -Dmaven.test.skip=true install'
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
