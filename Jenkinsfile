node {
   // Mark the code checkout 'stage'....
   stage 'Checkout code'

   // Get some code from a GitHub repository
   git url: 'https://github.com/bitnami/charts.git', branch: "helm-2.0"

   // Mark the code build 'stage'....
   stage 'Download latest helm'
   def helmObject = 'helm-v2.0.0-alpha.3-linux-amd64.tar'
   def helmUrl = "https://storage.googleapis.com/kubernetes-charts-ci/helm/${helmObject}"
   sh "wget -q ${helmUrl}"
   sh "tar xfv ${helmObject}"
   
   stage 'Install helm'
   sh './linux-amd64/helm init'
   
   git diff --name-only HEAD~1 HEAD 
   def applications = sh(script: "git diff --name-only HEAD~1 HEAD | awk -F/ '{print \$1}' | uniq", returnStdout: true).trim().split()
   
   applications.each() {
      stage 'Run lint on application'
      sh "./linux-amd64/helm lint ${it}"
      
      stage 'Install application'
      def releaseName = sh(script: "./linux-amd64/helm install ${application}", returnStdout: true).trim()
   
      stage 'Uninstall application'
      sh "./linux-amd64/helm delete ${releaseName}"
   }
   
   

}
