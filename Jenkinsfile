node {
    def registry = 'bulletinboard:1.0'
    stage('Checking out source from git repo') {
      echo 'Checkout complete'
      checkout scm
    }
    stage('Checking Docker build environment') {
      echo 'Docker environment check complete'
      sh 'git --version'
      echo "Branch: ${env.BRANCH_NAME}"
      sh 'docker -v'
    }
    
    stage('Building Docker for image bulletin board app') {
	    echo 'Building Docker image bulletin board app'
      withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
	     	sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPassword}"
	     	sh "docker build -t ${registry} ."
	     	sh "docker tag ${registry} ${env.dockerHubUser}/${registry}"
	     	sh "docker push ${env.dockerHubUser}/${registry}"
      }
    }
    
    stage('Deploying to TKG in AWS') {
      echo 'Deploying to TKG...'
      sh "kubectl apply -f ./bulletinboard.yaml"
      sh "kubectl get nodes"
      sh "kubectl get pods"
      sh "kubectl get svc"
    }
}
