//Declarative Groovy Script

node {
    stage("pull service code from gitHUb"){
        git 'https://github.com/vikash-kumar01/Linux-Git-Jenkins-Ansible-Dokcer-Kubernetes-DevOpsProj.git'
    }
    
    stage("Docker Buld Image"){
        //sh 'sudo chmod 777 /var/run/docker.sock' 
        sh 'docker image build -t $JOB_NAME:v1.$BUILD_ID .'
        sh 'docker image tag $JOB_NAME:v1.$BUILD_ID vikashashoke/$JOB_NAME:v1.$BUILD_ID'
        sh 'docker image tag $JOB_NAME:v1.$BUILD_ID vikashashoke/$JOB_NAME:latest'
    }
    
    stage("Docker image push : DockerHub"){
    /*before pushing need to login to docker hub but passing password like this isn't a correct way
      therefore, go to syntx generator and search for withcredentials() and choose binding as 
      secret text and add credentials & remember the id 
    */
    withCredentials([string(credentialsId: 'dockerhub_passwd', variable: 'dockerhub_passwd')]) {
    sh "docker login -u vikashashoke -p ${dockerhub_passwd}"
    sh 'docker image push vikashashoke/$JOB_NAME:v1.$BUILD_ID'
    sh 'docker image push vikashashoke/$JOB_NAME:latest'
    //A number of images will get stored into our jenkins server so need to remove prev build images
    //local images,taged images & latest images all delete 
    sh 'docker image rm $JOB_NAME:v1.$BUILD_ID vikashashoke/$JOB_NAME:v1.$BUILD_ID vikashashoke/$JOB_NAME:latest'
   }
    }
    stage("Docker Container Deployment")
    {
        def docker_run = 'docker run -p 9000:80 -d --name scripted-pipeline-demo vikashashoke/scripted-pipeline-demo:latest'
        def docker_rmv_container = 'docker rm -f scripted-pipeline-demo'
        def docker_rmi = 'docker rmi -f vikashashoke/scripted-pipeline-demo'
        // container deployment need to be done on remote host server DOCKER-Host so ssh-Agent plugin required in jenkins
       sshagent(['dockerhost_passwd']) {
    sh "ssh -o StrictHostKeyChecking=no ubuntu@172.31.15.56 ${docker_rmv_container}"
    sh "ssh -o StrictHostKeyChecking=no ubuntu@172.31.15.56 ${docker_rmi}"
    sh "ssh -o StrictHostKeyChecking=no ubuntu@172.31.15.56 ${docker_run}"
    //Once sucess try with Docker-Host_IP:Port
    //Once after Changing the Dockerfile it says port already allocated to prev container whom we didn't yet deleted
    // therefor ist delete the prev container so that port will be free then try creating a new 
    //But still it'll not update because latest image is already in server it's not going to dockerHub to pull latest commmit so need to remove images also
       }
    }
    
}
