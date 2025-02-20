pipeline {
    environment {
        registry = '9494482991/nginx'
        registryCredential = 'dockerhub'
        dockerSwarmManager = '10.0.1.200:2375'
        dockerhost = '10.0.1.200'
        dockerImage = ''
    }
    agent any
    stages {
        stage('Cloning our Git') {
            steps {
                git 'https://github.com/jpranayk/dockertest1.git'
            }
        }
        stage('Building our image') {
            steps {
                script {
                    dockerImage = docker.build registry + ":$BUILD_NUMBER"
                }
            }
        }
        stage('Push Image To DockerHUB') {
            steps {
                script {
                    docker.withRegistry( '', registryCredential ) {
                        dockerImage.push()
                    }
                }
            }
        }   
        stage('Deploying to Docker Swarm') {
            steps {
                sh "docker -H tcp://$dockerSwarmManager service rm testing1 || true"
                sh "docker -H tcp://$dockerSwarmManager service create --name nginx -p 8100:80 $registry:v$BUILD_NUMBER"
            }
        }
        stage('Verifying The Deployment') {
            steps {
                sh 'curl http://$dockerhost:8100 || exit 1'
                }
        }
    }
}
