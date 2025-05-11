node {
    stage('Code Checkout') {
        echo 'Code checkout from the git repo'
        git 'https://github.com/saranya-sreedharan/php-application'
    }

    stage('Building the application') {
        echo 'Moving inside the directory and then building the application'
        sh 'sudo docker build -t sarus23/my-php-app:1.0 .'
    }

    stage('Pushing docker image') {
        echo 'Pushing docker image to Docker Hub'
        def dockerHubCredentials = 'docker-hub-credentials'
        withCredentials([usernamePassword(credentialsId: dockerHubCredentials, usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
            sh 'sudo docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD'
            sh 'sudo docker push sarus23/my-php-app:1.0'
        }
    }
    
    stage ('Application Deployment'){
        echo 'Deploying the application in the server'
        sh 'docker run -d -p 8081:80 sarus23/my-php-app:1.0'
    } 
}
