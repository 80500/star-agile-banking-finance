pipeline {
    agent { label 'slave1' }	


	environment {	
		DOCKERHUB_CREDENTIALS=credentials('dockerloginid')
	} 
    
    stages {
        stage('SCM Checkout') {
            steps {
                // Get some code from a GitHub repository
                git 'https://github.com/80500/star-agile-banking-finance/tree/master'
            }
		}
        stage('Maven Build') {
            steps {
                // Run Maven on a Unix agent.
                sh "mvn -Dmaven.test.failure.ignore=true clean package"
            }
		}
       stage("Docker build"){
            steps {
				sh 'docker version'
				sh "docker build -t prajwal/bankapp-eta-app:${BUILD_NUMBER} ."
				sh 'docker image list'
				sh "docker tag prajwal/bankapp-eta-app:${BUILD_NUMBER} prajwal/bankapp-eta-app:latest"
            }
        }
		stage('Login2DockerHub') {

			steps {
				sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
			}
		}
		stage('Push2DockerHub') {

			steps {
				sh "docker push prajwal/bankapp-eta-app:latest"
			}
		}
        stage('Deploy to Kubernetes Environment') {
            steps {
		script {
		sshPublisher(publishers: [sshPublisherDesc(configName: 'Kubernetes-Cluster', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'kubectl apply -f kubernetesdeploy.yaml', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '.', remoteDirectorySDF: false, removePrefix: '', sourceFiles: 'kubernetesdeploy.yaml')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
		       }
            }
    	}
    }
}	
	
