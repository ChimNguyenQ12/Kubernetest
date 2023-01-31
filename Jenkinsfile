node {
    stage('Git Checkout'){
        git 'https://github.com/ChimNguyenQ12/Kubernetest.git'
    }
    
    stage('Sending Dockerfile to Ansible'){
        sshagent(['Ansible-Server']) {
          sh 'ssh -o StrictHostKeyChecking=no root@172.31.8.156'
          sh 'scp /var/lib/jenkins/workspace/kubernestest-demo/* root@172.31.8.156:/home/ubuntu/'
        }   
    }
    
    stage('Docker Build'){
        sshagent(['Ansible-Server']) {
          sh "ssh -o StrictHostKeyChecking=no root@172.31.8.156 'cd /home/ubuntu/ && docker build -t $JOB_NAME:v1.$BUILD_ID .'"
        }   
    }
    
    stage('Tagging + Push Image'){
        sshagent(['Ansible-Server']) {
          sh 'ssh -o StrictHostKeyChecking=no root@172.31.8.156 docker tag $JOB_NAME:v1.$BUILD_ID chimnguyen/$JOB_NAME:v1.$BUILD_ID '
          sh 'ssh -o StrictHostKeyChecking=no root@172.31.8.156 docker tag $JOB_NAME:v1.$BUILD_ID chimnguyen/$JOB_NAME:lastest '
          
          withCredentials([string(credentialsId: 'dockerhub_passwd', variable: 'dockerhub_passwd')]) {
            sh 'ssh -o StrictHostKeyChecking=no root@172.31.8.156 docker login -u chimnguyen -p ${dockerhub_passwd}'
            sh 'ssh -o StrictHostKeyChecking=no root@172.31.8.156 docker push chimnguyen/$JOB_NAME:v1.$BUILD_ID '
            sh 'ssh -o StrictHostKeyChecking=no root@172.31.8.156 docker push chimnguyen/$JOB_NAME:lastest '
          }
        }   
    }
}