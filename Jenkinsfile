pipeline {
    agent any
        environment {
            PROJECT_ID = 'halogen-emblem-261509'
            CLUSTER_NAME = 'k8s-cluster-1'
            LOCATION = 'europe-west4-a'
            CREDENTIALS_ID = 'kubernetes'
        }
    
    stages {
        stage('Scm Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build') {
            steps {
                echo 'Cleaning and packaging...'
                sh 'mvn clean package'
            }
        }
        stage('Test') {
            steps {
                echo 'Testing...'
                sh 'mvn test'
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    /* commented docker hub image building
                    myimage = docker.build("gagan0910/tomcat01:${env.BUILD_ID}")
                    */
                    myimage = docker.build("gcr.io/halogen-emblem-261509/gagan0910/tomcat01:${env.BUILD_ID}")
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                script {
                    /* commented docker hub registry push
                    docker.withRegistry('https://registry.hub.docker.com', 'b1454f0d-e3ba-4f09-85e7-4b07d2985823'){
                    myimage.push("${env.BUILD_ID}")
                  }
                  */
                    docker.withRegistry('https://gcr.io', 'gcr:gcr0910'){
                    myimage.push("${env.BUILD_ID}")
                  }
                }
            }
        }
        stage('Deploy to K8s') {
            steps {
                echo 'Deployment started...'
                sh 'ls -ltr'
                sh 'pwd'
                sh "sed -i 's/tagversion/${env.BUILD_ID}/g' deployment.yaml"
                step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
                echo 'Deployment finished.'
            }
        }
    }
}
