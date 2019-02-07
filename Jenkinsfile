node {
    currentBuild.result = "SUCCESS"

    try {

       stage('Checkout'){

          git credentialsId: '65633920-5eb0-45aa-a2a9-c8333b674be5', url: 'https://github.com/akkipuneet/nodejsapp.git'
       }

       stage('Test'){

         env.NODE_ENV = "test"

         print "Environment will be : ${env.NODE_ENV}"

         sh 'node -v'
         sh 'npm prune'
         sh 'npm install'
         sh 'npm test'

       }

       stage('Build Docker'){
            sh 'lastcommitsha=$(git rev-parse --verify HEAD) && docker build --build-arg version=$version_${BUILD_NUMBER} --build-arg lastcommitsha=$lastcommitsha -t puneetsingla/nodejsapp:$version_${BUILD_NUMBER} .'
       }
       
      stage('Push Image'){
         echo 'Push to Repo'
        withCredentials([usernamePassword(credentialsId: 'd25d8874-6f76-416e-8102-f0934448bb0a', passwordVariable: 'docker_password', usernameVariable: 'docker_username')])
		 {
		 sh 'docker login -u $docker_username  -p $docker_password'
		 }
         sh 'docker push puneetsingla/nodejsapp:$version_${BUILD_NUMBER}'
          
      }
      
       stage('Deploy'){

         echo 'ssh to web server and tell it to pull new image'
	     sh 'docker rm -f nodejs 2> nul'
         sh 'docker run --name=nodejs -d -p 8081:8080 puneetsingla/nodejsapp:$version${BUILD_NUMBER}'

       }

       stage('Cleanup'){

         echo 'prune and cleanup'
         sh 'npm prune'
         sh 'rm node_modules -rf'

  /*           mail body: 'project build successful',
                     from: 'xxxx@yyyyy.com',
                     replyTo: 'xxxx@yyyy.com',
                     subject: 'project build successful',
                     to: 'yyyyy@yyyy.com'  */
       }



    }
    catch (err) {

        currentBuild.result = "FAILURE"

  /*          mail body: "project build error is here: ${env.BUILD_URL}" ,
            from: 'xxxx@yyyy.com',
            replyTo: 'yyyy@yyyy.com',
            subject: 'project build failed',
            to: 'zzzz@yyyyy.com'   */

        throw err
    }

}
