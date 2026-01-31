pipeline {
    agent any

    environment {
        // Định nghĩa tên môi trường ảo
        VENV = 'venv'
    }

    stages {
        stage('Setup Environment') {
            steps {
                script {
                    // Tạo môi trường ảo nếu chưa có
                    sh "python3 -m venv ${VENV}"
                    
                    // Nâng cấp pip và cài đặt các thư viện cần thiết
                    sh """
                        . ${VENV}/bin/activate
                        pip install --upgrade pip
                        pip install pytest pytest-cov coverage
                    """
                }
            }
        }

        stage('Run Unit Tests') {
            steps {
                script {
                    // Chạy pytest với coverage cho file baitap1.py
                    // --junitxml giúp Jenkins đọc được kết quả test dễ dàng hơn
                    sh """
                        . ${VENV}/bin/activate
                        pytest --cov=baitap1 --cov-report=xml --cov-report=html --junitxml=results.xml baitap1.py
                    """
                }
            }
        }

        stage('Analyze Coverage') {
            steps {
                script {
                    // Kiểm tra báo cáo coverage trên console
                    sh """
                        . ${VENV}/bin/activate
                        coverage report -m
                    """
                }
            }
        }
    }

    post {
        always {
            // Hiển thị kết quả test trên giao diện Jenkins (Cần plugin JUnit)
            junit 'results.xml'
            
            // Lưu trữ báo cáo HTML để xem sau
            publishHTML(target: [
                allowMissing: false,
                alwaysLinkToLastBuild: true,
                keepAll: true,
                reportDir: 'htmlcov',
                reportFiles: 'index.html',
                reportName: 'Python Coverage Report'
            ])
        }
        success {
            echo 'Chúc mừng! Tất cả các bài test đã vượt qua.'
        }
        failure {
            echo 'Có lỗi xảy ra trong quá trình test. Vui lòng kiểm tra lại code.'
        }
    }
}