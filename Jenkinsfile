node {
    def app

    stage('Clone repository') {
        /* Let's make sure we have the repository cloned to our workspace */

        checkout scm
    }

    stage('Build image') {
        sh 'mvn clean install'
        
    
        /* This builds the actual image; synonymous to
         * docker build on the command line */

        app = docker.build("kartikjalgaonkar/hello-world")
    }

    stage('Push image') {
        /* Finally, we'll push the image with two tags:
         * First, the incremental build number from Jenkins
         * Second, the 'latest' tag.
         * Pushing multiple tags is cheap, as all the layers are reused. */
        docker.withRegistry('https://registry.hub.docker.com', 'docker_credentials') {
            app.push("${env.BUILD_NUMBER}")
            app.push("latest")
        }
    }
    
    stage('kubectl deploy'){
        sh 'minikube start'
       /* sh 'kubectl delete deployment hello-world'
        sh 'kubectl delete svc hello-world'*/
        sh 'kubectl run hello-world --replicas=2 --labels="run=load-balancer-example" --image=kartikjalgaonkar/hello-world  --port=8082'
        sleep 60
        sh 'kubectl get deployments hello-world'
        sh 'kubectl describe deployments hello-world'
        sh 'kubectl get replicasets'
        sh 'kubectl describe replicasets'
        sh 'kubectl expose deployment hello-world --type=LoadBalancer --name=my-service'
        sh 'kubectl get services my-service'
        sleep 100
        sh 'kubectl get services my-service'
        sh 'kubectl describe services my-service'
        sh 'kubectl get pods --output=wide'
        sh 'minikube service hello-world'
        sh 'minikube dashboard'        
    }
   
}
