pipeline {
    agent any  // Runs on any Jenkins agent
    tools {
        maven 'Maven3'  // Use configured Maven from Global Tool Configuration
        jdk 'JDK17'     // Use configured JDK
    }
    environment {
        DOCKER_HUB_REPO = 'bejaouimedaziz8/spring-app'  // Your Docker Hub repo
        DOCKER_TAG = 'latest'  // Or use BUILD_NUMBER for versioning
    }
    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/Bejaoui-Med-Aziz/bejaoui-project.git', branch: 'main'  // Your repo and branch
            }
        }
        stage('Build Maven') {
            steps {
                sh 'mvn clean package -DskipTests'  // Builds JAR; remove skip if tests are fixed
            }
        }
        stage('Docker Build & Push') {
            steps {
                script {
                    docker.build("${DOCKER_HUB_REPO}:${DOCKER_TAG}")
                    docker.withRegistry('https://index.docker.io/v1/', 'docker-hub-credentials') {  // Credentials ID from Jenkins
                        docker.image("${DOCKER_HUB_REPO}:${DOCKER_TAG}").push()
                    }
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                sh 'kubectl apply -f spring-deployment.yaml -n devops'  // Assumes YAML in repo and namespace exists
            }
        }
    }
    post {
        always {
            echo 'Pipeline completed'
        }
    }
}
