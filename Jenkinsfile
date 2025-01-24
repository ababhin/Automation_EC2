pipeline {
    agent any
    environment {
        ANSIBLE_HOST_KEY_CHECKING = "False"
        INVENTORY_FILE = "inventory.ini"
        PLAYBOOK = "playbook.yml"
        GIT_REPO = "https://github.com/ababhin/Automation_EC2.git"
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
                withCredentials([sshUserPrivateKey(credentialsId: 'hadoop-key', keyFileVariable: 'PEM_FILE')]) {
                    sh '''
                    ansible-playbook -i ${INVENTORY_FILE} ${PLAYBOOK} --private-key ${PEM_FILE} -vvvv
                    '''
                }
            }
        }
        stage('Verify Hadoop Installation') {
            steps {
                echo 'Verifying Hadoop Installation...'
                withCredentials([sshUserPrivateKey(credentialsId: 'hadoop-key', keyFileVariable: 'PEM_FILE')]) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no -i ${PEM_FILE} ec2-user@35.180.46.154 \
                        "jps && hdfs dfsadmin -report" || echo 'Verification failed!'
                    '''
                }
            }
        }
    }
    post {
        success {
            echo 'Pipeline completed successfully! Hadoop is set up.'
            slackSend(channel: '#build-notifications', message: 'Pipeline completed successfully! Hadoop is set up.')
        }
        failure {
            echo 'Pipeline failed. Please check the logs.'
            slackSend(channel: '#build-notifications', message: 'Pipeline failed. Please check the logs.')
        }
        always {
            cleanWs()
        }
    }
}
