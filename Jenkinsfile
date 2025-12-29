pipeline {
    agent none 
    stages {
        stage('Build') { 
            agent {
                docker {
                    image 'python:3.9' 
                }
            }
            steps {
                sh 'python -m py_compile sources/add2vals.py sources/calc.py' 
            }
        }
        stage('Test') {
            agent {
                docker {
                    image 'qnib/pytest'
                }
            }
            steps {
                sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
            }
            post {
                always {
                    junit 'test-reports/results.xml'
                }
            }
        }
        stages {
            stage('Install Dependencies') {
                steps {
                    // 在构建之前先安装 pyinstaller
                    sh 'pip install pyinstaller'
                }
            }
            stage('Deliver') {
                agent {
                    docker {
                        image 'cdrx/pyinstaller-linux:python2'
                    }
                }
                steps {
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
}