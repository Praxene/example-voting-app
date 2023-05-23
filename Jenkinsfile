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
        sh 'trivy fs -f json -o git-security.json .'
        sh 'trivy image -f json -o vote-container-security.json docker.io/spywash/devops:vote'
        sh 'trivy image -f json -o result-container-security.json docker.io/spywash/devops:result'
        sh 'trivy image -f json -o worker-container-security.json docker.io/spywash/devops:worker'
        
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
            archiveArtifacts artifacts: 'git-security.json, vote-container-security.json, result-container-security.json, worker-container-security.json', onlyIfSuccessful: false
        }
    }
}
