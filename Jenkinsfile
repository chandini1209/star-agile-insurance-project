pipeline {
    agent { label 'slave1' }

	environment {	
		DOCKERHUB_CREDENTIALS=credentials('dockerlogin')
	}
	
    stages {
        stage('SCM_Checkout') {
            steps {
               echo "Perform SCM Checkout"
			   git 'https://github.com/chandini1209/star-agile-health-care'			   
            }
        }
        stage('Application Build') {
            steps {
                echo "Perform Application Build"
				sh 'mvn clean package'
            }
        }
        stage('Build Docker Image') {
            steps {
				sh 'docker version'
		    	sh "docker build --no-cache -t chandini964/healthcare-eta-app:${BUILD_NUMBER} ."
				sh 'docker image list'
				sh "docker tag chandini964/healthcare-eta-app:${BUILD_NUMBER} chandini964/healthcare-eta-app:latest"
            }
        }
		stage('Login2DockerHub') {

			steps {
				sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
			}
		}
		stage('Publish_to_Docker_Registry') {
			steps {
				sh "docker push chandini964/healthcare-eta-app:latest"
			}
		}
        stage('Deploy to Kubernetes Cluster') {
            steps {
                script{
                   sshPublisher(publishers: [sshPublisherDesc(configName: 'KMaster', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'kubectl apply -f kubernetes.yaml', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '.', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '*.yaml')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
                }
            }
        }
    }
}
