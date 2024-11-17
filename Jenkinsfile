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
                    // 运行 pre-commit
                    sh './image_retrieval/bin/pre-commit run --all-files || true'
                    // 提交自动修复的更改
                    sh '''
                    if [ -n "$(git status --porcelain)" ]; then
                        git config user.name "Jenkins"
                        git config user.email "jenkins@example.com"
                        git add .
                        git commit -m "Auto-format code with black and isort"
                    fi
                    '''
                    // 再次运行 flake8 检查，确保格式修复后代码符合 PEP8
                    sh './image_retrieval/bin/flake8 . --exclude=image_retrieval --count --show-source --statistics'
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
