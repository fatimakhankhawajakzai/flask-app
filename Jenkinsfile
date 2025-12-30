pipeline {
    agent any

    environment {
        VENV_DIR = ".venv"
        DIST_DIR = "dist"
        DEPLOY_DIR = "deployed_app"
        ARTIFACT_NAME = "flask_app_%BUILD_NUMBER%.zip"
    }

    stages {

        stage('Clone Repository') {
            steps {
                checkout scm
                bat 'python --version'
                bat 'git --version'
            }
        }

        stage('Install Dependencies') {
            steps {
                bat '''
                    python -m venv %VENV_DIR%
                    %VENV_DIR%\\Scripts\\activate && python -m pip install --upgrade pip
                    %VENV_DIR%\\Scripts\\activate && pip install -r requirements.txt
                '''
            }
        }

        stage('Run Unit Tests') {
            steps {
                bat '''
                    %VENV_DIR%\\Scripts\\activate && pytest
                '''
            }
        }

        stage('Build Application') {
            steps {
                bat '''
                    if exist %DIST_DIR% rmdir /s /q %DIST_DIR%
                    mkdir %DIST_DIR%
                    powershell Compress-Archive -Path * -DestinationPath %DIST_DIR%\\%ARTIFACT_NAME% -Force
                '''
            }
        }

        stage('Deploy Application (Simulated)') {
            steps {
                bat '''
                    if exist %DEPLOY_DIR% rmdir /s /q %DEPLOY_DIR%
                    mkdir %DEPLOY_DIR%
                    copy %DIST_DIR%\\%ARTIFACT_NAME% %DEPLOY_DIR%\\
                    echo Deployment successful > %DEPLOY_DIR%\\deploy.log
                    dir %DEPLOY_DIR%
                '''
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'dist\\*.zip', fingerprint: true
        }
    }
}
