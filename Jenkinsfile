def containerName="akash-insurance"
def tag="latest"
def dockerHubUser="akashkbarik12"
def gitURL='https://github.com/akashkbarik/star-agile-insurance-project.git'

node {
      stage('checkout') {
	         git changelog: false, credentialsId: 'GitHubCreds', poll: false, url: 'https://github.com/akashkbarik/star-agile-insurance-project.git'
	  }
	  stage('build') {
	         sh "mvn clean install"
	  }
	  stage('clean any existing docker image'){
	         sh "docker image prune -f"
	  }
	  stage('build the docker image'){
	         sh "docker build -t $containerName:$tag  --pull --no-cache ."
			 echo  "*****image build sucessfully completed******"
	  }
	  stage('push to docker hub'){
	         withCredentials([usernamePassword(credentialsId: 'dockerhubcreds', passwordVariable: 'dockerHubPass', usernameVariable: 'dockerHubUser')]) {
                sh "docker login -u $dockerHubUser -p $dockerHubPass"
				sh "docker tag $containerName:$tag $dockerHubUser/$containerName:$tag"
				sh "docker push $dockerHubUser/$containerName:$tag"
				echo "***********image push sucessfully done*********"
				stash includes: '**/**.yml', name: 'service'
				
            }
	  }
}
node("k8sjenkins"){
      stage('deploy the container'){
          unstash 'service'
          
                sh "sudo kubectl get nodes"
                sh "sudo kubectl apply -f deployment.yml"
                sh "sudo kubectl apply -f service.yml"
	         
	    }
	  
      }
