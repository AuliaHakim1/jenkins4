pipeline {
    agent any

    environment {
        PYTHON = "C:\\Users\\DELL\\AppData\\Local\\Programs\\Python\\Python313\\python.exe"
        VENV_DIR = "venv"
    }

    stages {
        stage('Check Python') {
            steps {
                bat '"%PYTHON%" --version'
            }
        }

        stage('Setup Virtual Environment') {
            steps {
                bat '''
                if exist %VENV_DIR% rmdir /s /q %VENV_DIR%
                "%PYTHON%" -m venv %VENV_DIR%
                call %VENV_DIR%\\Scripts\\activate
                python -m pip install --upgrade pip
                '''
            }
        }

        stage('Install Dependencies') {
            steps {
                bat '''
                call %VENV_DIR%\\Scripts\\activate
                pip install -r requirements.txt
                '''
            }
        }

        stage('Run Tests') {
            steps {
                bat '''
                call %VENV_DIR%\\Scripts\\activate
                pytest test_app.py
                '''
            }
        }

        stage('Deploy') {
            when {
                anyOf {
                    branch 'main'
                    branch pattern: "release/.*", comparator: "REGEXP"
                }
            }
            steps {
                echo "🚀 Simulating deploy from branch ${env.BRANCH_NAME}"
            }
        }
    }

    post {
        success {
            script {
                def payload = [
                    content: "✅ Build SUCCESS on `${env.BRANCH_NAME}`\nURL: ${env.BUILD_URL}"
                ]
                httpRequest(
                    httpMode: 'POST',
                    contentType: 'APPLICATION_JSON',
                    requestBody: groovy.json.JsonOutput.toJson(payload),
                    url: 'https://discord.com/api/webhooks/1425150778540032061/e2K6vVbSN32B3KfONaua5sNDWb-Nsztag3JhEE_QasmXmfh7lXgiA_bR9C7yM9rQ5e1J'
                )
            }
        }

        failure {
            script {
                def payload = [
                    content: "❌ Build FAILED on `${env.BRANCH_NAME}`\nURL: ${env.BUILD_URL}"
                ]
                httpRequest(
                    httpMode: 'POST',
                    contentType: 'APPLICATION_JSON',
                    requestBody: groovy.json.JsonOutput.toJson(payload),
                    url: 'https://discord.com/api/webhooks/1425150778540032061/e2K6vVbSN32B3KfONaua5sNDWb-Nsztag3JhEE_QasmXmfh7lXgiA_bR9C7yM9rQ5e1J'
                )
            }
        }
    }
}
