import groovy.json.JsonSlurper

def getFtpPublishProfile(def publishProfilesJson) {
  def pubProfiles = new JsonSlurper().parseText(publishProfilesJson)
  for (p in pubProfiles)
    if (p['publishMethod'] == 'FTP')
      return [url: p.publishUrl, username: p.userName, password: p.userPWD]
}

node {
  withEnv(['AZURE_SUBSCRIPTION_ID=f85ec290-8150-4244-b20b-36e91cffee2b',
        'AZURE_TENANT_ID=9eb139b2-d39b-46df-8ab2-1a37fc6f9290']) {
    stage('init') {
      checkout scm
    }
  
    stage('build') {
      sh 'mvn clean package'
    }
  
    stage('deploy') {
      def resourceGroup = 'devsuryajavawebapp1'
      def webAppName = 'surya007'
      // login Azure
  //    withCredentials([usernamePassword(credentialsId: '123456789', passwordVariable: 'RGy8Q~ZZnTHDHRRFumRfCY5-FDLg8t4RRPoCnbI-', usernameVariable: 'b44f0492-2eb6-46da-aff0-f6f2c5477ed5')]) {
       sh '''
          az login --service-principal -u b44f0492-2eb6-46da-aff0-f6f2c5477ed5 -p RGy8Q~ZZnTHDHRRFumRfCY5-FDLg8t4RRPoCnbI- -t $AZURE_TENANT_ID
           az account set -s $AZURE_SUBSCRIPTION_ID
        '''
   //   }
      // get publish settings
      def pubProfilesJson = sh script: "az webapp deployment list-publishing-profiles -g devsuryajavawebapp2 -n surya007", returnStdout: true
      def ftpProfile = getFtpPublishProfile pubProfilesJson
      // upload package
      sh "curl -T target/calculator-1.0.war $ftpProfile.url/webapps/ROOT.war -u '$ftpProfile.username:$ftpProfile.password'"
      // log out
      sh 'az logout'
    }
  }
} 

// def pubProfilesJson = sh script: "az webapp deployment list-publishing-profiles -g $resourceGroup -n $webAppName", returnStdout: true
