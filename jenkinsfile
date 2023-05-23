pipeline {
  agent any
  stages {
//    stage('Snyk scan') {
//	  steps {
//		snykSecurity additionalArguments: '--all-projects', snykInstallation: 'snyk', snykTokenId: 'SNYK_TOKEN'
//	  }
//	}
    stage('Build result') {
      steps {
        sh 'docker build -t spywash/devops:result ./result'
      }
    } 
    stage('Build vote') {
      steps {
        sh 'docker build -t spywash/devops:vote ./vote'
      }
    }
    stage('Build worker') {
      steps {
        sh 'docker build -t spywash/devops:worker ./worker'
      }
    }
    stage('Security scan') {
      steps {
        sh 'trivy fs . > security.log'
        sh 'trivy image docker.io/spywash/devops:vote >> security.log'
        sh 'trivy image docker.io/spywash/devops:result >> security.log'
        sh 'trivy image docker.io/spywash/devops:worker >> security.log'
        
      }
    }
    stage('Push result image') {
      steps {
        withDockerRegistry(credentialsId: 'dockerhubcredentials', url: '') {
          sh 'docker push spywash/devops:result'
        }
      }
    }
    stage('Push vote image') {
      steps {
        withDockerRegistry(credentialsId: 'dockerhubcredentials', url: '') {
          sh 'docker push spywash/devops:vote'
        }
      }
    }
    stage('Push worker image') {
      steps {
        withDockerRegistry(credentialsId: 'dockerhubcredentials', url: '') {
          sh 'docker push spywash/devops:worker'
        }
      }
    }
    stage('Apply Kubernetes files') {
      steps {
         sh 'kubectl --kubeconfig=/kube/config apply -f kubedeploy2.yml '
      }
    }
  }
  post {
        always {
            archiveArtifacts artifacts: 'security.log', onlyIfSuccessful: false
        }
    }
}
