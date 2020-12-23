def commit_id
pipeline {
    agent any
    

    stages {
        stage('Preparation') {
            steps {
                checkout scm
                sh "git rev-parse --short HEAD > .git/commit-id"
                script {                        
                    commit_id = readFile('.git/commit-id').trim()
                }
            }
        }
        stage('Build') {
            steps {
                echo 'Building....'
                sh 'mvn clean install'
                echo 'build complete'
            }
        }
        stage('Image Build') {
            steps {
                echo 'Building docker image.............'
                sh "docker build -t houssemtebai/position-simulator:'${tag}' ./"
                echo 'build complete'
                echo 'pushing docker image to dockerhub.............'
                sh "docker push houssemtebai/position-simulator:'${tag}'"
                echo 'push complete'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying to Kubernetes'
                sh "sed -i -r 's|richardchesterwood/k8s-fleetman-position-simulator:release2|houssemtebai/position-simulator:${commit_id}|' workloads.yaml"
                sh "kubectl config set current-context minikube"
                sh 'kubectl get all'
                sh 'kubectl apply -f .'
                sh 'kubectl get all'
                echo 'deployment complete'
                
            }
        }
    }
}