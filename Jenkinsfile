pipeline {
    agent any
    environment {
        PIP_CACHE_DIR = "${WORKSPACE}/.pip-cache"
    }
    stages {
        stage('Checkout Code') {
            steps {
                sh 'echo Code checked out by SCM'
            }
        }
        stage('Setup Python Environment') {
            steps {
                dir("${WORKSPACE}") {
                    sh '/Users/emmayan/miniconda3/bin/python3 -m venv image_retrieval'
                    sh './image_retrieval/bin/pip install --upgrade pip'
                    sh './image_retrieval/bin/pip install -r requirements.txt'
                }
            }
        }
        stage('Run Pre-commit Checks') {
            steps {
                dir("${WORKSPACE}") {
                    // 运行 pre-commit 检查，包含 black 和 isort
                    sh './image_retrieval/bin/pre-commit run --all-files'
                }
            }
        }
        stage('Run Linting (PEP8)') {
            steps {
                dir("${WORKSPACE}") {
                    // 运行 flake8 检查
                    sh './image_retrieval/bin/flake8 . --exclude=image_retrieval --count --show-source --statistics'
                }
            }
        }
    }
    post {
        always {
            dir("${WORKSPACE}") {
                // 存档日志文件
                archiveArtifacts artifacts: '**/*.log', allowEmptyArchive: true
            }
            cleanWs()
        }
    }
}
