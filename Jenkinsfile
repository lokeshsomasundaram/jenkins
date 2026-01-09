pipeline {
    agent any

    environment {
        WORKER_IP    = "ec2-54-169-156-215.ap-southeast-1.compute.amazonaws.com"
        WORKER_USER  = "ubuntu"
        DEPLOY_DIR   = "/home/ubuntu/deploy"
        GIT_CRED     = "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"        // Jenkins GitHub PAT credential ID
        SSH_CRED     = "${env.SSHKEY}"    // Jenkins SSH key credential ID
        REPO_URL     = "https://github.com/lokeshsomasundaram/jenkins.git" 
        REPO_BRANCH  = "master"
    }

    stages {

        stage('Clone from GitHub') {
            steps {
                git branch: "${REPO_BRANCH}",
                    url: "${REPO_URL}",
                    credentialsId: "${GIT_CRED}"
            }
        }
        
        stage('Prepare Deployment Folder on Worker') {
            steps {
                sshagent(credentials: ["ubuntu"]) {
                    sh """
                        ssh-keyscan -t rsa example.com >> ~/.ssh/known_hosts
                        ssh ${WORKER_USER}@${WORKER_IP} 'ls -lh'
                    """
                }
            }
        }
        
        stage('Copy Website Files to Worker') {
            steps {
                sshagent(credentials: ["${SSH_CRED}"]) {
                    sh """
                    # Replace ./ with the correct folder if website files are inside a subfolder
                    scp -o StrictHostKeyChecking=no -r ./* ${WORKER_USER}@${WORKER_IP}:${DEPLOY_DIR}
                    """
                }
            }
        }

        stage('Deploy to Nginx') {
            steps {
                sshagent(credentials: ["${SSH_CRED}"]) {
                    sh """
                    ssh -o StrictHostKeyChecking=no ${WORKER_USER}@${WORKER_IP} '
                        sudo cp -r ${DEPLOY_DIR}/* /var/www/html/ &&
                        sudo chown -R www-data:www-data /var/www/html &&
                        sudo systemctl restart nginx &&
                        rm -rf ${DEPLOY_DIR}
                    '
                    """
                }
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
