pipeline {
    agent any
    environment {
        ANSIBLE_HOST_KEY_CHECKING = "False"    // Disable SSH key host checking for simplicity
        INVENTORY_FILE = "inventory.ini"      // Inventory file for Ansible
        PLAYBOOK = "playbook.yml"             // Ansible playbook file
        PEM_FILE = "/path/to/your-key.pem"    // Path to your .pem private key
        GIT_REPO = "https://github.com/ababhin/Automation_EC2.git" // GitHub repo URL
    }
    stages {
        stage('Clone Repository') {
            steps {
                echo 'Cloning GitHub Repository...'
                git branch: 'main', url: "${GIT_REPO}"
            }
        }
        stage('Verify Ansible Installation') {
            steps {
                echo 'Verifying Ansible Installation...'
                sh '''
                if ! command -v ansible-playbook >/dev/null; then
                    echo "Ansible is not installed or not in PATH."
                    exit 1
                fi
                ansible --version
                '''
            }
        }
        stage('Run Ansible Playbook') {
            steps {
                echo 'Running Ansible Playbook...'
                sh '''
                ansible-playbook -i ${INVENTORY_FILE} ${PLAYBOOK} --private-key ${PEM_FILE}
                '''
            }
        }
        stage('Verify Installation') {
            steps {
                echo 'Verifying Installation...'
                sh '''
                ssh -o StrictHostKeyChecking=no -i ${PEM_FILE} ec2-user@<EC2-PUBLIC-IP> "systemctl status amazon-cloudwatch-agent"
                '''
            }
        }
    }
    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Please check the logs.'
        }
        always {
            cleanWs() // Clean up the workspace after the build
        }
    }
}
