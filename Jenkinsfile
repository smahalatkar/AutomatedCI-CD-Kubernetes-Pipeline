pipeline {
	agent any
	tools {
		jdk 'localJDK'
		maven 'localMaven'
	}
	stages {
		stage("Maven-Build"){
			steps {
				sh 'mvn -Dmaven.test.skip=true install'
			}
		}
		stage ("Docker-Login") {
			steps {
				sh 'docker login -u 96637 -p password@123'
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
				sh "docker tag search:${env.BUILD_ID} 96637/search:0.${env.BUILD_ID}"
				sh "docker tag website:0.${env.BUILD_ID} 96637/website:0.${env.BUILD_ID}"
			}
		}
		stage ("Pushing-Images-to-Registry"){
			steps {
				sh "docker push 96637/search:0.${env.BUILD_ID}"
				sh "docker push 96637/website:0.${env.BUILD_ID}"
			}
		}
		stage('DeployToProduction') {
            		steps {
                		kubernetesDeploy(
                    			kubeconfigId: 'kubeconfigazure',
                    			configs: 'flight-booking-kube.yml'
                		)
            		}
        	}
	}
}
