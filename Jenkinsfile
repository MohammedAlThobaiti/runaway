pipeline {

	agent any

	environment {
  AWS_ACCESS_KEY_ID     = credentials('Mohammed-aws-secret-key-id')
  AWS_SECRET_ACCESS_KEY = credentials('Mohammed-aws-secret-access-key')
  DOCKER_HUB_TOKEN = credentials('MohammedAlThobaiti-dockerhub-token')
		ARTIFACT_NAME = 'Dockerrun.aws.json'
		AWS_S3_BUCKET = 'mohammedalthobaiti-belt2-artifacts-123456'
		AWS_EB_APP_NAME = 'MohammedAlThobaiti'
        AWS_EB_ENVIRONMENT_NAME = 'Mohammedalthobaiti-env'
        AWS_EB_APP_VERSION = "${BUILD_ID}"
	}

	stages {

		stage('Build') {

			steps {
				sh 'docker build -t "mohammedalthobaiti/runaway:latest" RUNaWAY/'
			}
		}

		stage('Login') {

			steps {
				sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
			}
		}

		stage('Push') {

			steps {
				sh 'docker push mohammedalthobaiti/runaway:latest'
			}
		}

        stage('Deploy') {
            steps {
                sh 'aws configure set region us-east-1'
                sh 'aws elasticbeanstalk create-application-version --application-name $AWS_EB_APP_NAME --version-label $AWS_EB_APP_VERSION --source-bundle S3Bucket=$AWS_S3_BUCKET,S3Key=$ARTIFACT_NAME'
                sh 'aws elasticbeanstalk update-environment --application-name $AWS_EB_APP_NAME --environment-name $AWS_EB_ENVIRONMENT_NAME --version-label $AWS_EB_APP_VERSION'
            }
	}
    }
	post {
		always {
			sh 'docker logout'
		}
	}

}