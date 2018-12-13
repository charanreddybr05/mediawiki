pipeline {
  environment {
    registry = "charanreddybr05/mediawiki"
    registryCredential = 'dockerhub'
  }
  agent any
  stages {
	stage('Cloning Git') {
		steps {
			git ‘https://github.com/charanreddybr05/mediawiki.git'
			}
		}
    stage('Building image') {
      steps{
        script {
          docker.build registry + ":$BUILD_NUMBER"
        }
      }
    }
	stage('Deploy Image') {
	  steps{
		script {
			docker.withRegistry( ‘’, registryCredential ) {
			dockerImage.push()
			}
		}
	}
	}
	stage('') {
		steps{
			script {
				sh('sed -i 's/VERSION/$BUILD_NUMBER/g')
				kubectl create -f mediawiki-mysql-deployment.yaml
			}
		}
	}
  }
}