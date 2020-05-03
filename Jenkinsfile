#!groovy

import groovy.json.JsonSlurperClassic

node {

	def SF_CONSUMER_KEY="3MVG97quAmFZJfVzkjkH9WQ5jyxFX1gDe4k7Zrk9xNibhMofHH0BNMNO__ElvDwvL_x41w3QI3m8m.FnyffJm"
	def SF_USERNAME="mohankvmr@salesforce.com"
    def SF_INSTANCE_URL = "https://login.salesforce.com"
    def SERVER_KEY_CREDENTALS_ID = "2f36509c-c3a5-41b9-a7e3-5e9dd1b5991a"
    def toolbelt = tool 'toolbelt'


    // -------------------------------------------------------------------------
    // Check out code from source control.
    // -------------------------------------------------------------------------

    stage('checkout source') {
        checkout scm
    }


    // -------------------------------------------------------------------------
    // Run all the enclosed stages with access to the Salesforce
    // JWT key credentials.
    // -------------------------------------------------------------------------
    
    withEnv(["HOME=${env.WORKSPACE}"]) {
        
        withCredentials([file(credentialsId: SERVER_KEY_CREDENTALS_ID, variable: 'server_key_file')]) {

            // -------------------------------------------------------------------------
            // Authorize the Dev Hub org with JWT key and give it an alias.
            // -------------------------------------------------------------------------

            stage('Authorize DevHub') {
                rc = command "${toolbelt}/sfdx force:auth:jwt:grant --instanceurl ${SF_INSTANCE_URL} --clientid ${SF_CONSUMER_KEY} --username ${SF_USERNAME} --jwtkeyfile ${server_key_file} --setdefaultdevhubusername --setalias HubOrg"
                if (rc != 0) {
                    error 'Salesforce dev hub org authorization failed.'
                }
            }
        }
    }
}

def command(script) {
    if (isUnix()) {
        return sh(returnStatus: true, script: script);
    } else {
        return bat(returnStatus: true, script: script);
    }
}
