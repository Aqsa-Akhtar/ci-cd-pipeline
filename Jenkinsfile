pipeline {
    agent any

    environment {
        NODE_ENV = 'test'
    }

    tools {
        nodejs 'NodeJS 18' // Set up this tool in Jenkins
    }

    triggers {
        githubPush() // Enables auto-build on push
    }

    stages {
        stage('Checkout Code') {
            steps {
                git 'https://github.com/your-username/ci-cd-pipeline.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Lint Check') {
            steps {
                sh 'npm run lint || true'  // ESLint errors won't stop the pipeline yet
            }
        }

        stage('Run Tests') {
            steps {
                sh 'npm test'
            }
        }

        stage('Check Code Coverage') {
            steps {
                script {
                    def coverage = readFile('coverage/coverage-summary.json')
                    def data = new groovy.json.JsonSlurper().parseText(coverage)
                    def pct = data.total.lines.pct
                    if (pct < 80) {
                        error("Code coverage below 80%! Found: ${pct}%")
                    }
                }
            }
        }

        stage('Archive Artifacts') {
            when {
                expression { return env.BUILD_TAG?.contains("release") }
            }
            steps {
                archiveArtifacts artifacts: '**/*.js', fingerprint: true
            }
        }
    }

    post {
        failure {
            emailext(
                to: '232977@students.au.edu.pk',
                subject: "Build Failed: ${env.JOB_NAME} - ${env.BUILD_NUMBER}",
                body: "Check Jenkins job ${env.JOB_NAME} at ${env.BUILD_URL}",
                from: 'jenkins@gmail.com'
            )
        }
    }
}
