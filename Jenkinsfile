pipeline {
    agent {
        node {
          label 'add jenkins node name'
         }
    }
    
       stages {

        stage('Print IP Address and Hostname') {
            steps {
                script {
                    // Execute shell commands to retrieve IP address and hostname
                    def ipAddress = sh(script: 'curl ifconfig.me', returnStdout: true).trim()
                    def hostname = sh(script: 'hostname', returnStdout: true).trim()

                    // Print the IP address and hostname
                    echo "IP Address: ${ipAddress}"
                    echo "Hostname: ${hostname}"
                }
            }
        }
        stage('Print System Information') {
            steps {
                script {
                    // Execute shell commands to retrieve system information
                    def cpuDetails = sh(script: 'lscpu', returnStdout: true).trim()
                    def memoryInfo = sh(script: 'free -h', returnStdout: true).trim()
                    def diskSpace = sh(script: 'df -h', returnStdout: true).trim()
                    // Print CPU details
                    echo "CPU Details:"
                    echo cpuDetails
                    // Print memory information
                    echo "Memory Information:"
                    echo memoryInfo
                    // Print disk space
                    echo "Disk Space:"
                    echo diskSpace
                }
            }
        }
        stage('Clone Code') {
            steps {
                script {
                    def workspacePath = '/home/intel/workspace'
                    dir(workspacePath) {
                        git 'https://github.com/stereolabs/zed-docker.git'
                        env.WORKSPACE = workspacePath
                    }
                }
            }
        }

        stage('Docker Login') {
            steps {
                sh 'docker login -u pradeei -p dckr_pat_BY8afnlGmK9BbqvSKMy4bHNs3S0'
            }
        }

        stage('Build Docker Image') {
            steps {
                dir(env.WORKSPACE + '/legacy/2.8/ubuntu1804/cuda10.2/devel') {
                    sh 'docker build -t pradeei/chatgpt:latest .'
                }
            }
        }

        stage('Push Image') {
            steps {
                sh 'docker push pradeei/chatgpt:latest'
            }
        }


    stage('Run Sanity Tests') {
             steps {
                // Run pytest inside a Docker container
                sh 'docker run --rm pradeei/chatgpt:latest pytest'
        }
    }
}
}
