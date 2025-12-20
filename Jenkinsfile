pipeline {
    agent any

    tools {
        jdk 'java-11'
        maven 'maven'
    }

    stages {

        stage('Git Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Rajavardhanrg/CI-CD-project-with-jenkins.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean install -DskipTests'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t rajavardhanrg30/ci-cd-app:1 .'
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-credentials',
                    usernameVariable: 'DOCKER_USERNAME',
                    passwordVariable: 'DOCKER_PASSWORD'
                )]) {
                    sh '''
                    echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin
                    '''
                }
            }
        }

        stage('Push Docker Image to DockerHub') {
            steps {
                sh 'docker push rajavardhanrg30/ci-cd-app:1'
            }
        }

        stage('Run Container') {
            steps {
                sh '''
                docker stop ci-cd-c1 || true
                docker rm ci-cd-c1 || true
                docker run -d --name ci-cd-c1 -p 9001:8080 rajavardhanrg30/ci-cd-app:1
                '''
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                dir('/var/lib/jenkins/workspace/raju-ci-cd') {
					 withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'k8s', namespace: '', restrictKubeConfigAccess: false, serverUrl: '') {
					 sh '''
					 kubectl delete --all pods
					 kubectl apply -f deployment.yaml
					 kubectl apply -f service.yaml
					 '''
					 } 
				}
			}	
		}
    }
}
