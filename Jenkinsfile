pipeline {
    agent any

    stages {

        stage('Checkout SCM') {
            steps {
                echo 'Source code retrieved from GitHub by Jenkins SCM'
            }
        }

        stage('Build Application') {
            steps {
                bat 'npm install'
            }
        }

        stage('SAST Scan') {
            when {
                expression { false }
            }
            steps {
                withSonarQubeEnv('SonarQube') {
                    script {
                        def scannerHome = tool 'SonarQubeScanner'
                        bat "${scannerHome}\\bin\\sonar-scanner.bat -Dsonar.projectKey=juice-shop-devsecops -Dsonar.sources=."
                    }
                }
            }
        }

        stage('Dependency Check') {
            when {
                expression { false }
            }
            steps {
                dir('D:/DevSecOps/juice-shop-lab/juice-shop') {
                    dependencyCheck additionalArguments: '--scan . --format XML --format HTML', odcInstallation: 'DependencyCheck'
                    dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
                }
            }
        }

        stage('Docker Build') {
            when {
                expression { false }
            }
            steps {
                bat 'docker build -t juice-shop-devsecops .'
            }
        }

        stage('Trivy Scan') {
            steps {
                bat 'trivy image --format table -o trivy-report.txt juice-shop-devsecops:latest'
                bat 'type trivy-report.txt'
            }
        }

        stage('Checkov Scan') {
            when {
                expression {
                    def order = [
                        'Build Application': 1,
                        'SAST Scan': 2,
                        'Dependency Check': 3,
                        'Docker Build': 4,
                        'Trivy Scan': 5,
                        'Checkov Scan': 6,
                        'Docker Run': 7,
                        'ZAP Scan (DAST)': 8
                    ]
                    return order[params.START_FROM] <= order['Checkov Scan']
                }
            }
            steps {
                bat '"C:\\Users\\USER\\AppData\\Local\\Programs\\Python\\Python313\\Scripts\\checkov.cmd" -d . > checkov-report.txt'
                bat 'type checkov-report.txt'
            }
        }

        stage('Docker Run') {
            steps {
                bat 'docker rm -f juice-shop-container || exit /b 0'
                bat 'docker run -d --name juice-shop-container -p 3000:3000 juice-shop-devsecops:latest'
            }
        }

        stage('ZAP Scan (DAST)') {
            steps {
                echo 'ZAP DAST stage to be configured'
            }
        }
    }
}
