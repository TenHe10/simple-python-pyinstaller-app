pipeline {
    agent none 
    stages {
        stage('Build') { 
            agent { docker { image 'python:3.9' } }
            steps {
                sh 'python -m py_compile sources/add2vals.py sources/calc.py' 
            }
        }
        
        stage('Test') {
            agent { docker { image 'qnib/pytest' } }
            steps {
                // 确保目录存在，防止 pytest 报错
                sh 'mkdir -p test-reports'
                sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
            }
            post {
                always {
                    junit 'test-reports/results.xml'
                }
            }
        }

        stage('Deliver') {
            agent {
                docker {
                    image 'python:3.9' // 使用官方镜像，环境更透明
                }
            }
            steps {
                // 1. 更新 pip 并安装 pyinstaller
                sh 'pip install --upgrade pip'
                sh 'pip install pyinstaller'
                
                // 2. 使用 python -m 方式调用，避免 PATH 找不到命令的问题
                sh 'python -m PyInstaller --onefile sources/add2vals.py'
            }
            post {
                success {
                    // 确认文件存在再归档
                    archiveArtifacts 'dist/add2vals'
                }
            }
        }
    }
}