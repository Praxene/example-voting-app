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
        sh 'trivy fs . > git-security.log'
        sh 'trivy image docker.io/spywash/devops:vote > vote-security.log'
        sh 'trivy image docker.io/spywash/devops:result > result-security.log'
        sh 'trivy image docker.io/spywash/devops:worker > worker-security.log' 
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
    stage('Deploy Kubernetes updates') {
      steps {
        //Mise à jour du worker
         sh 'kubectl set image deployments/worker worker=docker.io/spywash/devops:worker '
        //Mise à jour de résult
         sh 'kubectl set image deployments/result result=docker.io/spywash/devops:result'
        //Mise à jour de vote
         sh 'kubectl set image deployments/vote vote=docker.io/spywash/devops:vote'
      }
    }
  }
  post {
        always {
            archiveArtifacts artifacts: 'git-security.log, vote-security.log, result-security.log, worker-security.log', onlyIfSuccessful: false
        }
    }
}
