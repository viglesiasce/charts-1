node {
   sh "env"
   // Mark the code checkout 'stage'....
   stage 'Checkout code'
   def branch = "origin/helm-2.0"
   // Get some code from a GitHub repository
   checkout scm

   // Mark the code build 'stage'....
   stage 'Download latest helm'
   def helmObject = 'helm-v2.0.0-alpha.3-linux-amd64.tar'
   def helmUrl = "https://storage.googleapis.com/kubernetes-charts-ci/helm/${helmObject}"
   sh "wget -q ${helmUrl}"
   sh "tar xfv ${helmObject}"
   
   stage 'Install helm'
   sh './linux-amd64/helm init'
   
   def applications = sh(script: "git diff --name-only ${branch} | grep / | awk -F/ '{print \$1}' | uniq", returnStdout: true).trim().split()
   
  for (int i = 0; i < applications.size(); i++) {
      def application = applications[i]
      stage 'Run lint on application'
      sh "./linux-amd64/helm lint ${application}"
      
      stage 'Install application'
      def releaseName = sh(script: "./linux-amd64/helm install ${application}", returnStdout: true).trim()
   
      stage 'Uninstall application'
      sh "./linux-amd64/helm delete ${releaseName}"
   }

}
