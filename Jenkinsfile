pipeline {
	agent any
	tools {
		jdk 'localJDK'
		maven 'localMaven'
	}
	stages {
		stage("Maven-Build"){
			steps {
				withEnv(['PATH+EXTRA=/usr/sbin:/usr/bin:/sbin:/bin'])
				sh 'mvn -Dmaven.test.skip=true install'
			}
		}
		stage ("Docker-Login") {
			steps {
				withEnv(['PATH+EXTRA=/usr/sbin:/usr/bin:/sbin:/bin'])
				sh 'docker login -u satyendrasingh -p Password@123'
			}
		}
		stage ("Deleting-Previous-Docker-Builds"){
			steps {
				withEnv(['PATH+EXTRA=/usr/sbin:/usr/bin:/sbin:/bin'])
				sh 'docker rmi -f "$(docker images -aq)"'
			}
		}
		stage ("Docker-Build-Services") {
			steps {
				withEnv(['PATH+EXTRA=/usr/sbin:/usr/bin:/sbin:/bin'])
				sh "docker build -t search:0.${env.BUILD_ID} search/"
				withEnv(['PATH+EXTRA=/usr/sbin:/usr/bin:/sbin:/bin'])
				sh "docker build -t website:0.${env.BUILD_ID} website/"
			}
		}
		stage ("Tagging-docker-images") {
			steps {
				withEnv(['PATH+EXTRA=/usr/sbin:/usr/bin:/sbin:/bin'])
				sh "docker tag search:0.${env.BUILD_ID} satyendrasingh/search:0.${env.BUILD_ID}"
				withEnv(['PATH+EXTRA=/usr/sbin:/usr/bin:/sbin:/bin'])
				sh "docker tag website:0.${env.BUILD_ID} satyendrasingh/website:0.${env.BUILD_ID}"
			}
		}
		stage ("Pushing-Images-to-Registry"){
			steps {
				withEnv(['PATH+EXTRA=/usr/sbin:/usr/bin:/sbin:/bin'])
				sh "docker push satyendrasingh/search:0.${env.BUILD_ID}"
				withEnv(['PATH+EXTRA=/usr/sbin:/usr/bin:/sbin:/bin'])
				sh "docker push satyendrasingh/website:0.${env.BUILD_ID}"
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
