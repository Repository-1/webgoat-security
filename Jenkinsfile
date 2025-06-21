pipeline {
    agent 
    {
    docker {
        image 'jenkins-micromamba-agent:latest'
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
                sh 'mvn clean compile -DskipTests'
            }
        }
        
        stage('Security Scan') {
            steps {
                sh 'semgrep --config=auto --json --output=semgrep.json . || true'
                sh 'bandit -r . -f json -o bandit.json || true'
                // Archive artifacts in the same stage
                archiveArtifacts artifacts: '*.json', allowEmptyArchive: true
            }
        }
    }
}
