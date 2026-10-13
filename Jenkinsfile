pipeline {
    agent any
    stages {
        stage("Setup Environment") {
            steps {
                sh '''
                    echo "Checking Docker version and access..."
                    docker --version
                    docker ps || true
                '''
            }
        }
        stage("Image build and Push") {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub-repo', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                    sh "docker image build -t khanwaseeque123s/myimage:t${BUILD_NUMBER} -f Dockerfile.waseek.xyz ."
                    sh "echo $PASS | docker login -u $USER --password-stdin"
                    sh "docker image push khanwaseeque123s/myimage:t${BUILD_NUMBER}"
                }
            }
        }
        stage('Deploy on Production Server') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'prodserver123',
                        usernameVariable: 'SSH_USER',
                        passwordVariable: 'SSH_PASS'
                    )
                ]) {
                    sh '''
                        set +x

                        sshpass -p "$SSH_PASS" ssh \
                            -o StrictHostKeyChecking=no \
                            -o UserKnownHostsFile=/dev/null \
                            "$SSH_USER@103.216.171.222" \
                            "docker container rm -f waseek.xyz || true"
                            
                        # Deploy the Docker container with correct --name flag
                        sshpass -p "$SSH_PASS" ssh \
                            -o StrictHostKeyChecking=no \
                            -o UserKnownHostsFile=/dev/null \
                            "$SSH_USER@103.216.171.222" \
                            "docker run --name waseek.xyz -d -p 3000:80 khanwaseeque123s/myimage:t${BUILD_NUMBER}"
                    '''
                }
            }
        }
    }
}
