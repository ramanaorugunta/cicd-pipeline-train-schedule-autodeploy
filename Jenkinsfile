pipeline {
    agent { label 'slave1' }
	
	environment {	
		DOCKERHUB_CREDENTIALS=credentials('dockerloginid')
	}

    stages {
        stage('SCM_Checkout') {
            steps {
                echo 'Perform SCM_Checkout'
				git 'https://github.com/ramanaorugunta/cicd-pipeline-train-schedule-autodeploy.git'
            }
        }        
        stage('Build Docker Image') {
            steps {
				sh 'docker version'
				sh "docker build -t oruguntaramana/ci-cd-train-schedule:${BUILD_NUMBER} ."
				sh 'docker image list'
				sh "docker tag oruguntaramana/ci-cd-train-schedule:${BUILD_NUMBER} oruguntaramana/ci-cd-train-schedule:latest"
            }
        }
		stage('Login2DockerHub') {

			steps {
				sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
			}
		}
		stage('Publish_to_Docker_Registry') {
			steps {
				sh "docker push oruguntaramana/ci-cd-train-schedule:latest"
			}
		}
        stage('Deploy to Kubernetes') {
            steps {
                script{
                    sshPublisher(publishers: [sshPublisherDesc(configName: 'Kubernetes_Master', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'kubectl apply -f train-schedule-kube.yml', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '.', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '*.yml')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
                }
            }
        }		
    }
}
