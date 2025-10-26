pipeline {
    agent any
    
    stages {
        stage('拉取代码') {
            steps {
                // 多分支流水线会自动拉取代码，这里主要是确认
                echo "当前分支: ${env.BRANCH_NAME}"
                sh 'ls -la'  // 验证文件是否存在
            }
        }
        
        stage('Build') {
            steps {
                sh '''
                    cd ui
                    npm install
                    npm run build
                    cd ..
                    python3 -m venv venv
                    . ./venv/bin/activate
                    pip install -r requirements.in
                    pip install Flask flask-cors flask-sock pywebview
                    rm -rf dist
                    pip install pyinstaller
                    pyinstaller webui_zip_for_linux.spec
                '''
            }
        }
        
        stage('执行upgrade.sh') {
            steps {
                script {
                    sh script: '''
                        chmod +x upgrade.sh
                        ./upgrade.sh
                    ''', returnStdout: false
                }
            }
        }
    }
    
    post {
        always {
            echo "构建完成 - 状态: ${currentBuild.result}"
        }
        success {
            echo " 所有步骤执行成功！"
        }
        failure {
            echo " 构建失败，请检查日志"
        }
    }
}