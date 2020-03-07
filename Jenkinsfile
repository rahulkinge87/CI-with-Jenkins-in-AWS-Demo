pipeline {

    agent any

    environment {

        PROJECT_ID = 'devopspipeline-1'

        CLUSTER_NAME = 'k8-cluster'

        LOCATION = 'us-central1-c'

        CREDENTIALS_ID = 'k8'

    }

    stages {

        stage("Checkout code") {

            steps {

                checkout scm

            }

        }

		 stage("Build") {

            steps {

               echo "cleaning and packaging"

			   sh 'mvn clean package'

            }

        }

		 stage("Test") {

            steps {

                echo "Testing"

			   sh 'mvn test'

            }

        }

        stage("Build image") {

            steps {

                script {

                    /* myapp = docker.build("rahulkinge/devops:${env.BUILD_ID}") */
		       myapp = docker.build("gcr.io/devopspipeline-1/rahulkinge/devops:${env.BUILD_ID}")
	            


                }

            }

        }

        stage("Push image") {

            steps {

                script {

                   /* docker.withRegistry('https://registry.hub.docker.com', 'docker') */
		      docker.withRegistry('https://gcr.io', 'gcpcred') {

                            myapp.push("${env.BUILD_ID}")

                    }

                }

            }

        }        

        stage('Deploy to GKE') {

            steps{

			    echo "Deployment started"

				sh 'ls -ltr'

				sh 'pwd'

                sh "sed -i 's/tagversion/${env.BUILD_ID}/g' deployment.yaml"

                step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])

				echo "Deployment Finished"

            }

        }

    }    

}
