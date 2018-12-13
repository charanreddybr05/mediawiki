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
	stage('Installing Docker') {
      steps{
        script {
          //sh('apt-get install docker && service docker start')
		  sh('curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add - && echo "deb [arch=amd64] https://download.docker.com/linux/ubuntu xenial stable" >> /etc/apt/sources.list.d/additional-repositories.list && echo "deb http://ftp-stud.hs-esslingen.de/ubuntu xenial main restricted universe multiverse" >> /etc/apt/sources.list.d/official-package-repositories.list && apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 437D05B5 && apt-get update && apt-get -y install docker-ce')
        }
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
				//sh('sed -i 's/VERSION/$BUILD_NUMBER/g')
				//kubectl create -f mediawiki-mysql-deployment.yaml
				echo('Hello')
			}
		}
	}
  }
}