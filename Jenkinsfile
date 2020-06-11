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
    
    stage('Building image bulletin board app') {
	    echo 'Building Docker image bulletin board app...'
      withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
	     	sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPassword}"
	     	sh "docker build -t ${registry} bulletin-board-app/."
	     	sh "docker tag ${registry} ${env.dockerHubUser}/${registry}"
	     	sh "docker push ${env.dockerHubUser}/${registry}"
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
