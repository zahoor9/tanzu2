node {
    def registry = 'bulletinboard:1.0'
    stage('Check out source / git repo') 
    {
      echo 'Check out source / git repo'
      checkout scm
    }
    stage('Check Docker build env') 
    {
      echo 'Check Docker build env'
      sh 'git --version'
      echo "Branch: ${env.BRANCH_NAME}"
      sh 'docker -v'
    }
   
    stage('Building Docker image') 
    {
	    echo 'Building Docker image for bulletin board app'
      withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
	     	sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPassword}"
	     	sh "docker build -t ${registry} ."
	     	sh "docker tag ${registry} ${env.dockerHubUser}/${registry}"
	     	sh "docker push ${env.dockerHubUser}/${registry}"
      }
    }
    
    stage('Deploying to TKG in AWS') {
      echo 'Deploying to Tanzu Kubernetes Grid in AWS'
      sh "kubectl vsphere login --server=192.168.50.1 --tanzu-kubernetes-cluster-name gc1 --tanzu-kubernetes-cluster-namespace namespace1 --vsphere-username administrator@vsphere.local --insecure-skip-tls-verify"
      sh "kubectl version"
      sh "kubectl apply -f ./bulletinboard.yaml"
      sh "kubectl get nodes"
      sh "kubectl get pods"
      sh "kubectl get svc"
    }
}
