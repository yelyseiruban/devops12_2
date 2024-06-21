pipeline {
    agent any

    environment {
        SONARQUBE_JDBC_URL = 'jdbc:h2:tcp://sonarqube:9092/sonar'
        SONARQUBE_HOST_URL = 'http://sonarqube:9000'
        NUM1 = '10'
        NUM2 = '20'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Compile C++ application') {
            steps {
                sh 'g++ add_numbers.cpp -o add_numbers'
            }
        }

        stage('Run Application') {
            steps {
                sh './add_numbers $NUM1 $NUM2'
            }
        }


        stage('Unit Tests') {
            steps {
                sh 'your_unit_tests_command_here'
            }
        }

        stage('Test Coverage') {
            steps {
                sh 'your_code_coverage_command_here'
                publishHTML target: [reportDir: 'coverage', reportFiles: 'index.html']
            }
        }

        stage('Static Code Analysis') {
            steps {
                sh 'cppcheck your_source_code_directory'
                script {
                    sh 'cppcheck --xml your_source_code_directory > cppcheck_result.xml'
                    recordIssues tool: cppCheck(pattern: 'cppcheck_result.xml')
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('Sonar') {
                    sh 'sonar-scanner'
                }
            }
        }
    }
}
