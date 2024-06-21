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
                sh 'g++ -fprofile-arcs -ftest-coverage add_numbers.cpp -o add_numbers'
            }
        }

        stage('Run Application') {
            steps {
                sh './add_numbers $NUM1 $NUM2'
            }
        }


        stage('Unit Tests') {
                    steps {
                        script {
                            sh '''
                                #!/bin/bash

                                # Run the add_numbers program with two arguments and check the result
                                run_test() {
                                    local result=$(./add_numbers "$1" "$2" | awk '{print $NF}')
                                    local expected="$3"

                                    if [ $result -eq $expected ]; then
                                        echo "Test Passed: $1 + $2 = $3"
                                    else
                                        echo "Test Failed: $1 + $2 expected $3, but got $result"
                                    fi;
                                }

                                # Test cases
                                run_test 3 2 5
                                run_test 2 6 8
                                run_test 2 3 5
                            '''
                        }
                    }
                }

         stage('Test Coverage') {
                    steps {
                        sh 'gcov add_numbers.cpp'
                        sh 'lcov --capture --directory . --output-file coverage.info'
                        sh 'genhtml coverage.info --output-directory coverage'
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
