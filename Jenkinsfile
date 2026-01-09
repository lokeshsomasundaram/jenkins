pipeline {
    agent any

    environment {
        WORKER_IP    = "54.169.156.215"
        WORKER_USER  = "ubuntu"
        DEPLOY_DIR   = "/home/ubuntu/deploy"
        GIT_CRED     = "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"        // Jenkins GitHub PAT credential ID
        
        REPO_URL     = "https://github.com/lokeshsomasundaram/jenkins.git" 
        REPO_BRANCH  = "master"
    }

    stages {

        stage('Clone from GitHub') {
            steps {
                git branch: "${REPO_BRANCH}",
                    url: "${REPO_URL}",
                    credentialsId: "${GIT_CRED}"
                echo "hai"
            }
        }

    }

    post {
        success {
            echo "✅ Deployment Successful! Website is live."
        }
        failure {
            echo "❌ Deployment Failed! Check logs."
        }
    }
}
