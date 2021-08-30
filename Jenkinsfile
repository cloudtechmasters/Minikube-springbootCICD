pipeline {
    agent any
	environment {
		registry = 'cloudtechmasters/springboot-k8s:latest'
		registryCredentials = 'docker-credentials'
		dockerImage = ''
	}
    stages {
        stage ('SCM') {
            steps {
                checkout([$class: 'GitSCM', 
                	branches: [[name: '*/main']], doGenerateSubmoduleConfigurations: false, 
                	extensions: [], 
                	submoduleCfg: [], 
                	userRemoteConfigs: [[url: 'https://github.com/cloudtechmasters/springbootCICD-demo.git']]])
            }
        }
        stage ('Build Artifact') {
            steps {
                sh 'mvn clean install'
            }
        }
        stage ('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build registry
                }
            }
        }
        stage ('Docker Push') {
            steps {
                script {
                    docker.withRegistry('',registryCredentials) {
                        dockerImage.push()
                    }
                }
            }
        }
        stage ('Deploy in K8s') {
            steps {
                withCredentials([kubeconfigFile(credentialsId: 'kube-credentials', variable: 'KUBECONFIG')]) {
                	sh 'sudo kubectl apply -f deployement.yml'
                }
            }
        }
    }
}
