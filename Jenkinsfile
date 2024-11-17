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
                    withCredentials([usernamePassword(credentialsId: 'GitHub-token', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                        // 设置 Git 用户信息和远程仓库 URL
                        sh '''
                        git config user.name "Jenkins"
                        git config user.email "jenkins@example.com"
                        git remote set-url origin https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/your-org/your-repo.git
                        '''
                        // 提交并推送更改
                        sh '''
                        if [ -n "$(git status --porcelain)" ]; then
                            git add .
                            git commit -m "Auto-format code with black and isort"
                            git push origin HEAD
                        fi
                        '''
                    }
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
