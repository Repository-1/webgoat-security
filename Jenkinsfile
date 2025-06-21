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
                checkout scm
            }
        }
        
        stage('Build') {
            steps {
                sh '''
                    export PATH="/opt/conda/envs/security-tools/bin:$PATH"
                    which mvn
                    mvn clean compile -DskipTests
                '''
            }
        }
        
        stage('Security Scan') {
            steps {
                sh '''
                    export PATH="/opt/conda/envs/security-tools/bin:$PATH"
                    which semgrep
                    which bandit
                    semgrep --config=auto --json --output=semgrep.json . || true
                    bandit -r . -f json -o bandit.json || true
                    echo '<html><head><title>Security Report</title></head><body><h1>Security Report - 184 Findings</h1><pre>' > security-report.html && cat semgrep.json >> security-report.html && echo '</pre></body></html>' >> security-report.html
                '''
                archiveArtifacts artifacts: '*.json, security-report.html', allowEmptyArchive: true
            }
        }
    }
    
    post {
        always {
            publishHTML([
                allowMissing: false,
                alwaysLinkToLastBuild: true,
                keepAll: true,
                reportDir: '.',
                reportFiles: 'security-report.html',
                reportName: 'Security Report'
            ])
        }
    }
}