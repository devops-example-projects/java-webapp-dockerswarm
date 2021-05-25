node
{
    def buildNumber = BUILD_NUMBER
    stage("Code Checkout")
    {
        git branch: 'main', changelog: false, poll: false, url: 'https://github.com/devops-example-projects/java-webapp-dockerswarm.git'
    }
    stage("Code Build using Maven")
    {
        def mavenHome = tool name: 'Maven3.8.1', type: 'maven'
        def mavenCMD = "${mavenHome}/bin/mvn"
        sh "${mavenCMD} clean package"
    }
    stage("Build Docker Image")
    {
        sh "docker build -t adityadevops/java-webapp-dockerswarm:${buildNumber} ."
    }
    stage("Push Image to Docker Hub")
    {
        withCredentials([string(credentialsId: 'DockerHub', variable: 'DockerHub')]) 
        {
            sh "docker login -u adityadevops -p ${DockerHub}"    
        }
        sh "docker push adityadevops/java-webapp-dockerswarm:${buildNumber}"
    }
    stage("Run Docker Image in Docker Swarm Master")
    {
        sshagent(['DockerSwarmMaster']) 
        {
            sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.69.177 docker service rm webapp || true'
            sh "ssh -o StrictHostKeyChecking=no ubuntu@172.31.69.177 docker service create --name webapp -p 8085:8080 --replicas 2 adityadevops/java-webapp-dockerswarm:${buildNumber}"
        }
    }
}
