node {
    def app
    def test_cont_id

    stage('Clone repository') {
        /* Let's make sure we have the repository cloned to our workspace */

        checkout scm
    }

    stage('Build image') {
        /* This builds the actual image; synonymous to
         * docker build on the command line */
        sh 'echo starting'
        sh 'cat README.md'
        app = docker.build("ozhank/docker-test")
    }

    stage('Test image') {
        /* Ideally, we would run a test framework against our image.
         * Just an example */
        
        //sh 'docker run -p 80:80 -d ozhank/docker-test:latest'
        
        test_cont_id = sh (script: 'docker run -p 80:80 -d ozhank/docker-test:latest', returnStdout: true).trim()
        echo "Container ID: ${test_cont_id}"
        sh 'sleep 10'
        sh 'curl http://localhost'
        sh (script: "docker stop ${test_cont_id}", returnStdout: true).trim()
        sh (script: "docker rm ${test_cont_id}", returnStdout: true).trim()
        //sh 'sleep 240'
        /*app.inside {
            sh 'sleep 120'
            sh 'echo "Tests passed"'
            sh 'curl http://localhost:80 || exit 1'
        }*/
    }

     stage('Push image') {
        /* Finally, we'll push the image with two tags:
         * First, the incremental build number from Jenkins
         * Second, the 'latest' tag.
         * Pushing multiple tags is cheap, as all the layers are reused. */
        docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
            app.push("${env.BUILD_NUMBER}")
            //app.push("latest")
        }
    }
    
    stage('Removing image') {
        sh "docker rmi ${app.id}"
        sh "docker rmi registry.hub.docker.com/ozhank/docker-test:${env.BUILD_NUMBER}"
    }
    
    
    
}
