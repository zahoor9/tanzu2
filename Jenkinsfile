node {
    def registry = 'bulletinboard:1.0'
    stage('Checking out git repo') {
      echo 'Checkout...'
      checkout scm
    }
    stage('Checking environment') {
      echo 'Checking environment...'
      sh 'git --version'
      echo "Branch: ${env.BRANCH_NAME}"
      sh 'docker -v'
    }
    stage("Linting") {
      echo 'Linting...'
      sh '/usr/local/bin/hlint Dockerfile'
    }
    stage('Building image bulletin board app') {
	    echo 'Building Docker image bulletin board app...'
      withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
	     	sh "sudo docker login -u ${env.dockerHubUser} -p ${env.dockerHubPassword}"
	     	sh "sudo docker build -t ${registry} bulletin-board-app/."
	     	sh "sudo docker tag ${registry} ${env.dockerHubUser}/${registry}"
	     	sh "sudo docker push ${env.dockerHubUser}/${registry}"
      }
    }
    
    stage('Deploying to TKG') {
      echo 'Deploying to TKG...'
      sh "kubectl apply -f ./bulletinboard.yaml"
      sh "kubectl get nodes"
      sh "kubectl get pods"
      sh "kubectl get svc"
    }
}
