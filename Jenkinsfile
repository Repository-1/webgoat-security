pipeline {
    agent 
    {
    docker {
        image 'jenkins-micromamba-agent:latest'
    }
}
    stages {
        stage('Build') {
            steps {
                sh 'mvn clean compile -DskipTests'
            }
        }
        
        stage('Security Scan') {
            steps {
                sh 'semgrep --config=auto --json --output=semgrep.json . || true'
                sh 'bandit -r . -f json -o bandit.json || true'
            }
        }
    }
    
    post {
        always {
            archiveArtifacts artifacts: '*.json', allowEmptyArchive: true
        }
    }
}
