#!groovy

import groovy.json.JsonSlurperClassic

environment {
       env.PATH = env.PATH + ";c:\\Windows\\System32"
       
       println env.PATH
   }

node {
    
    
    // Below Credentials for Linux
    //def SF_CONSUMER_KEY="3MVG97quAmFZJfVzkjkH9WQ5jy_8vXQlA0Siq6lLRwqdzMuLzPDCdqCWl.CwSv2oXARvus95rogbGmCVrn203"
    //def SF_USERNAME="mohankvmr@salesforce.com"
    //def SF_INSTANCE_URL = "https://login.salesforce.com"
    //def SERVER_KEY_CREDENTALS_ID = "c26f254d-f219-43b7-8921-e8039d4a6abc"
    
    
    // Below Credentials for Windows
    /*def SF_CONSUMER_KEY="3MVG97quAmFZJfVzkjkH9WQ5jy41kIBuz0nzEJPhjnwV9p1iQmyocXSf0b33UBVnaQw0TxqcFLfQL9ongUHFH"
    def SF_USERNAME="mohankvmr@salesforce.com"
    def SF_INSTANCE_URL = "https://login.salesforce.com"
    def SERVER_KEY_CREDENTALS_ID = "628c1dee-16a0-41a2-bc89-ecb0acce2d8e"*/
       
    def HUB_ORG="mohankvmr@salesforce.com"
    def SFDC_HOST = "https://login.salesforce.com"
    def JWT_KEY_CRED_ID = "628c1dee-16a0-41a2-bc89-ecb0acce2d8e"
    def CONNECTED_APP_CONSUMER_KEY="3MVG97quAmFZJfVzkjkH9WQ5jy41kIBuz0nzEJPhjnwV9p1iQmyocXSf0b33UBVnaQw0TxqcFLfQL9ongUHFH"
    
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
    
    //withEnv(["HOME=${env.WORKSPACE}"]) {
        
       /* withCredentials([file(credentialsId: SERVER_KEY_CREDENTALS_ID, variable: 'server_key_file')]) {

            // -------------------------------------------------------------------------
            // Authorize the Dev Hub org with JWT key and give it an alias.
            // -------------------------------------------------------------------------

            stage('Authorize DevHub') {
                rc = command "${toolbelt} force:auth:jwt:grant --instanceurl ${SF_INSTANCE_URL} --clientid ${SF_CONSUMER_KEY} --username ${SF_USERNAME} --jwtkeyfile ${server_key_file} --setdefaultdevhubusername --setalias HubOrg"
                if (rc != 0) {
                    error 'Salesforce dev hub org authorization failed.'
                }
            }
        }*/
    //}
       
       withCredentials([file(credentialsId: JWT_KEY_CRED_ID, variable: 'jwt_key_file')]) {
        stage('Create Scratch Org') {
            if (isUnix()) {
                rc = sh returnStatus: true, script: "${toolbelt} force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile ${jwt_key_file} --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
            }else{
                 rc = bat returnStatus: true, script: "\"${toolbelt}\" force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile \"${jwt_key_file}\" --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
            }
            if (rc != 0) { error 'hub org authorization failed' }

            // need to pull out assigned username
              if (isUnix()) {
                rmsg = sh returnStdout: true, script: "${toolbelt} force:org:create --definitionfile config/enterprise-scratch-def.json --json --setdefaultusername"
              }else{
                   rmsg = bat returnStdout: true, script: "\"${toolbelt}\" force:org:create --definitionfile config/project-scratch-def.json --json --setdefaultusername"
              }
            printf rmsg
            println('Hello from a Job DSL script!')
            println(rmsg)
            def beginIndex = rmsg.indexOf('{')
            def endIndex = rmsg.indexOf('}')
            println(beginIndex)
            println(endIndex)
            def jsobSubstring = rmsg.substring(beginIndex)
            println(jsobSubstring)
            
            def jsonSlurper = new JsonSlurperClassic()
            def robj = jsonSlurper.parseText(jsobSubstring)
            //if (robj.status != "ok") { error 'org creation failed: ' + robj.message }
            SFDC_USERNAME=robj.result.username
            robj = null
            
        }
}

 

/*def command(script) {
    if (isUnix()) {
        println 'IsUnix'
        return sh(returnStatus: true, script: script);
    } else {
        return bat(returnStatus: true, script: script);
    }
}*/
