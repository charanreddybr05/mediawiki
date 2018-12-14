pipeline {
  environment {
    registry = "charanreddybr05/mediawiki"
    registryCredential = 'dockerhub'
  }
  agent any
  stages {
	stage('Cloning Git') {
		steps {
			git 'https://github.com/charanreddybr05/mediawiki.git'
			}
		}
    stage('Creating Secret and Building image') {
      steps{
        script {
		  kubectl create secret generic db-user-pass --from-file=./username.txt --from-file=./password.txt
          docker.build registry + ":$BUILD_NUMBER"
        }
      }
    }
	stage('Push Image') {
	  steps{
		script {
			docker.withRegistry( ‘’, registryCredential ) {
			dockerImage.push()
			}
		}
	}
	}
	stage('K8S Deply') {
		steps{
			script {
				//sh('sed -i 's/MVERSION/$BUILD_NUMBER/g')
				//kubectl create -f mediawiki-mysql-deployment.yaml
				echo('Hello')
			}
		}
	}
	stage('Deploy to dev') {
            steps {
                script {
					kubectl create namespace development
					kubectl create namespace STAGE
					kubectl create namespace UAT
                    namespace = 'development'
			
                    echo "Deploying application Mediawiki to ${namespace} namespace"
                    createNamespace (namespace)
					sh('sed -i 's/MVERSION/$BUILD_NUMBER/g')
					kubectl create -f mediawiki-mysql-deployment.yaml
					echo "Successfully deployed in DEV"
                }
            }
        }
	stage('Run Tests') {
            steps {
                script {
                    echo "Running Test"
                }
            }
        }
	stage('Approval Board to deploy in Stage Environment'){
    script {
        approvalMap = input id: 'deploy', message: 'Proceed with deployment or stop the build?', ok: 'Proceed Next?', parameters: [choice(choices: 'Proceed Deployment\nStop', description: 'please select Deploy to proceed or fail?', name: 'Action')], submitterParameter: 'APPROVER'
				
        if ("${approvalMap['Action']}" == 'Proceed Deployment') {
            echo "Deploying in STAGE"
			namespace = 'stage'
			echo "Deploying application Mediawiki to ${namespace} namespace"
            createNamespace (namespace)
			//sh('sed -i 's/MVERSION/$BUILD_NUMBER/g')
			kubectl create -f mediawiki-mysql-deployment.yaml
			echo "Successfully deployed in STAGE"
        }
        if ("${approvalMap['Action']}" == 'nStop') {
			echo "Not proceeding with deployment in STAGE becasue of some failure in development environment"
			script {
				sh ('exit 1')

		}
        }
        }
	}
	stage('Approval Board to deploy in UAT Environment'){
    script {
        approvalMap = input id: 'deploy', message: 'Proceed with deployment or stop the build?', ok: 'Proceed Next?', parameters: [choice(choices: 'Proceed Deployment\nStop', description: 'please select Deploy to proceed or fail?', name: 'Action')], submitterParameter: 'APPROVER'
				
        if ("${approvalMap['Action']}" == 'Proceed Deployment') {
            echo "Deploying in UAT"
			namespace = 'uat'
			echo "Deploying application Mediawiki to ${namespace} namespace"
            createNamespace (namespace)
			//sh('sed -i 's/MVERSION/$BUILD_NUMBER/g')
			kubectl create -f mediawiki-mysql-deployment.yaml
			echo "Successfully deployed in UAT"
        }
        if ("${approvalMap['Action']}" == 'nStop') {
			echo "Not proceeding with deployment in UAT becasue of some failure in STAGE environment"
			script {
				sh ('exit 1')

		}
        }
        }
	}
  }
}