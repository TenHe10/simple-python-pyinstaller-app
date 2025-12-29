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
                    // 如果你的代码是 Python 3，确保使用 python3 标签
                    image 'cdrx/pyinstaller-linux:python3' 
                }
            }
            steps {
                // 直接运行，跳过 pip install
                sh 'pyinstaller --onefile sources/add2vals.py'
            }
            post {
                success {
                    archiveArtifacts 'dist/add2vals'
                }
            }
        }
    }
}