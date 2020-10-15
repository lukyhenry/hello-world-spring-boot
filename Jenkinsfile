pipeline {
    agent any
    environment {
        PROJECT_ID = 'molten-enigma-268209'
        CLUSTER_NAME = 'my-first-cluster-1'
        LOCATION = 'us-central1-c'
        CREDENTIALS_ID = 'molten-enigma-268209'
    }
    stages {
        stage("Checkout code") {
            steps {
                checkout scm
            }
        }
        stage("Build image") {
            steps {
                script {
                    myapp = docker.build("asia.gcr.io/molten-enigma-268209/myproject:${env.BUILD_ID}")
					// TO TEST WEBHOOK
                }
            }
        }
        stage("Push image") {
            steps {
                script {
					withCredentials([file(credentialsId: jenkinsServiceAccountKey, variable: 'serviceaccountkey')]) {
						docker.withRegistry('https://asia.gcr.io', 'gcr:[serviceaccountkey]') {
								myapp.push("latest")
								myapp.push("${env.BUILD_ID}")
						}
					}
				}
			}
		}			
        stage('Deploy to GKE') {
            steps{
                sh "sed -i 's/myproject:latest/myproject:${env.BUILD_ID}/g' deployment.yaml"
                step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
            }
        }
    }    
}