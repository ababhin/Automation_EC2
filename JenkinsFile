pipeline {
    agent any
    stages {
        stage('Clone Repo') {
            steps {
                git branch: 'main', url: 'https://github.com/your-repo.git'
            }
        }
        stage('Run Ansible Playbook') {
            steps {
                sh '''
                ansible-playbook -i inventory.ini playbook.yml
                '''
            }
        }
    }
}
