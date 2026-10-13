pipeline{
   agent any
   stages{
     stage(""){
        steps{
           sh '''
                 apt update -y
                 apt upgrade -y
                 apt install vim sshpass sudo docker.io docker-compose -y
                 service docker start
                 service docker status
            '''
        }
     }
    stage("Image build and Push"){
       steps{
           withCredentials([usernamePassword(credentialsId: 'docker-hub-repo', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
              sh 'docker image build -t oklabs/myimage:t${BUILD_NUMBER} -f Dockerfile.devopsstudy.in .'
              sh "echo $PASS | docker login -u $USER --password-stdin"
              sh 'docker image push oklabs/myimage:t${BUILD_NUMBER}'
          }
       }
   }
   stage('Deploy on Production Server') {
    steps {
        withCredentials([
            usernamePassword(
                credentialsId: 'prodserver222',
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
                    "docker container rm -f devopsstudy"
                    
                # Deploy the Docker container
                sshpass -p "$SSH_PASS" ssh \
                    -o StrictHostKeyChecking=no \
                    -o UserKnownHostsFile=/dev/null \
                    "$SSH_USER@103.216.171.222" \
                    "docker run -name devopsstudy -d -p 3000:80 oklabs/myimage:t${BUILD_NUMBER}"
            '''
        }
    }
}
   }
}
