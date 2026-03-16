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
            steps {
                dir('D:/DevSecOps/juice-shop-lab/juice-shop') {
                    dependencyCheck additionalArguments: '--scan . --format XML --format HTML', odcInstallation: 'DependencyCheck'
                    dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
                }
            }
        }

        stage('Docker Build') {
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
            steps {
                bat '"C:\\Users\\USER\\AppData\\Local\\Programs\\Python\\Python313\\Scripts\\checkov.cmd" -d . > checkov-report.txt'
                bat 'type checkov-report.txt'
            }
        }

        stage('Docker Run') {
            steps {
                echo 'Docker run stage to be configured'
            }
        }

        stage('ZAP Scan (DAST)') {
            steps {
                echo 'ZAP DAST stage to be configured'
            }
        }
    }
}
