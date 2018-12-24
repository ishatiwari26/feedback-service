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

        app = docker.build("kartikjalgaonkar/feedback-service")
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
        /*sh 'minikube start'*/
        sh 'minikube --vm-driver=kvm start'
        /*sh 'kubectl delete deployment feedback-service'
        sh 'kubectl delete svc feedback-service'*/
        sh 'kubectl run feedback-service --replicas=2 --labels="run=load-balancer-example" --image=kartikjalgaonkar/feedback-service  --port=8084'
        sleep 60
        sh 'kubectl get deployments feedback-service'
        sh 'kubectl describe deployments feedback-service'
        sh 'kubectl get replicasets'
        sh 'kubectl describe replicasets'
        sh 'kubectl expose deployment feedback-service --type=LoadBalancer --name=my-feedback-service'
        sh 'kubectl get services my-feedback-service'
        sleep 100
        sh 'kubectl get services my-feedback-service'
        sh 'kubectl describe services my-feedback-service'
        sh 'kubectl get pods --output=wide'
        sh 'minikube service my-feedback-service'
        sh 'minikube dashboard'        
    }
   
}
