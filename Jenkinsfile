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
                    // 注意：你之前用了 python2 的镜像，如果代码是 python3 的，建议换成 python3 版
                    image 'cdrx/pyinstaller-linux:python3' 
                }
            }
            steps {
                // 在这个特定容器里安装并运行 pyinstaller
                sh 'pip install pyinstaller'
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