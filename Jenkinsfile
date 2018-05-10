//def gitCommit() {
  //  sh "git rev-parse HEAD > GIT_COMMIT"
    //def gitCommit = readFile('GIT_COMMIT').trim()
    //sh "rm -f GIT_COMMIT"
    //return gitCommit
//}

def gitCommit() {
    sh "git rev-parse HEAD > GIT_COMMIT-${env.BUILD_NUMBER}"
    def gitCommit = readFile("GIT_COMMIT-${env.BUILD_NUMBER}").trim()
    sh "rm -f GIT_COMMIT-${env.BUILD_NUMBER}"
    return gitCommit
}

node {

    // Checkout source code from Githhhhhhhh
    stage 'Checking out scm for repository'
    checkout scm
    stage '(TEST) unit/integration testing'
   // sh 'make test'
    stage '(BUILD) building image'
    sh "docker build -t dev.artifactory01.hds.local/nginx:${gitCommit()} ."
   // sh "docker login -u admin -p 'Hitachi1' dev.artifactory01.hds.local"
    stage '(PUBLISH) Pushing the image '
    sh "docker push dev.artifactory01.hds.local/nginx:${gitCommit()}"
     stage '(DEPLOY) Deploying the container'
    marathon(
        url: 'http://172.29.133.15:8080',
        forceUpdate: true,
        filename: 'marathon.json',
        appId: 'blog',
        docker: "dev.artifactory01.hds.local/nginx:${gitCommit()}".toString()
    )
   
        stage 'Collect test reports'
        
         sh 'touch reports/*.xml'
         junit '**/reports/*.xml'
       // step([$class: 'JUnitResultArchiver', testResults: '**/reports/*.xml'])
        stage 'Clean up'
       
                         
   

}
