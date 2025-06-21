pipeline {
    agent {
        docker {
            image 'jenkins-micromamba-agent:latest'
            args '--entrypoint="" -w /home/mambauser'
        }
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm  // Explicit checkout of your repository
            }
        }
        
        stage('Build') {
            steps {
                sh '''
                    eval "$(micromamba shell hook --shell bash)"
                    micromamba activate security-tools
                    mvn clean compile -DskipTests
                '''
            }
        }
        
        stage('Security Scan') {
            steps {
                sh '''
                    eval "$(micromamba shell hook --shell bash)"
                    micromamba activate security-tools
                    semgrep --config=auto --json --output=semgrep.json . || true
                    bandit -r . -f json -o bandit.json || true
                '''
                archiveArtifacts artifacts: '*.json', allowEmptyArchive: true
            }
        }
    }  // ← Only ONE closing brace for stages block
}