
pipeline {
    agent any

    environment {
        GIT_REPO_URL = 'https://github.com/Salhianis1/mern-application.git'
        GIT_BRANCH = 'main'
        SONARQUBE_ENV = 'SonarQube'
        SONAR_PROJECT_KEY = 'MERN-App'
        DOCKERHUB_USERNAME = 'salhianis20'  // Replace with your actual Docker Hub username
    }

    stages {
        stage('Clone Repository') {
            steps {
                echo "Cloning from ${env.GIT_REPO_URL}"
                git branch: "${env.GIT_BRANCH}", url: "${env.GIT_REPO_URL}"
            }
        }

        // stage('SonarQube Scan') {
        //     steps {
        //         script {
        //             withCredentials([string(credentialsId: 'SonarQube-ID', variable: 'jenkins')]) {
        //                 withSonarQubeEnv("${env.SONARQUBE_ENV}") {
        //                     sh """
        //                         sonar-scanner \
        //                         -Dsonar.projectKey=${SONAR_PROJECT_KEY} \
        //                         -Dsonar.sources=. \
        //                         -Dsonar.token=${jenkins}
        //                     """
        //                 }
        //             }
        //         }
        //     }
        // }

        // stage('Quality Gate (Non-blocking)') {
        //     steps {
        //         script {
        //             try {
        //                 timeout(time: 5, unit: 'MINUTES') {
        //                     def qualityGate = waitForQualityGate()
        //                     echo "Quality Gate status: ${qualityGate.status}"
        //                 }
        //             } catch (err) {
        //                 echo "Quality Gate check skipped or failed: ${err}"
        //             }
        //         }
        //     }
        // }

        stage('Build Frontend Docker Image') {
            steps {
                script {
                    sh "docker build -t ${DOCKERHUB_USERNAME}/mern-application-frontend ./mern/frontend"
                }
            }
        }

        stage('Build Backend Docker Image') {
            steps {
                script {
                    sh "docker build -t ${DOCKERHUB_USERNAME}/mern-application-backend ./mern/backend"
                }
            }
        }

        stage('Push Docker Images to Docker Hub') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'dockerhub-id', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        sh """
                            echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                            docker push ${DOCKERHUB_USERNAME}/mern-application-frontend
                            docker push ${DOCKERHUB_USERNAME}/mern-application-backend
                            docker logout
                        """
                    }
                }
            }
        }

        stage('Scan Docker Images with Trivy') {
            steps {
                script {
                    sh """
                        mkdir -p trivy-reports
                        trivy image --severity HIGH,CRITICAL --format table -o trivy-reports/frontend_scan.txt ${DOCKERHUB_USERNAME}/frontend
                        trivy image --severity HIGH,CRITICAL --format table -o trivy-reports/backend_scan.txt ${DOCKERHUB_USERNAME}/backend
                    """
                }
            }
        }
    } // closes stages
} // closes pipeline

