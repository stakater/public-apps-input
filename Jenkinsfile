#!/usr/bin/groovy
@Library('github.com/stakater/fabric8-pipeline-library@master')

String outputRepo = "git@github.com:stakater/stackator-infra-apps-output"
String outputRepoBranch = "master"
String outputRepoDir = "/home/jenkins/stackator-infra-apps-output"

String outputGitUser = "stakater-user"
String outputGitEmail = "stakater@gmail.com"

def utils = new io.fabric8.Utils()

toolsWithExternalKubeNode(toolsImage: 'stakater/pipeline-tools:1.5.1') {
    container(name: 'tools') {
        withCurrentRepo { def repoUrl, def repoName, def repoOwner, def repoBranch ->
            String workspaceDir = WORKSPACE
            String manifestsDir = workspaceDir + "/manifests/"

            // Slack variables
            def slackChannel = "${env.SLACK_CHANNEL}"
            def slackWebHookURL = "${env.SLACK_WEBHOOK_URL}"
            
            def git = new io.stakater.vc.Git()
            def slack = new io.stakater.notifications.Slack()
            def landscaper = new io.stakater.charts.Landscaper()
            def helm = new io.stakater.charts.Helm()

            try {
                stage('Init Helm') {
                    // Sleep is needed for the first time because tiller pod might not be ready instantly
                    helm.init(false)
                    
                    sh "sleep 30s"
                    
                    helm.addRepo("chartmuseum", "http://chartmuseum")
                }

                stage('Dry Run Charts') {
                    landscaper.apply(manifestsDir, true)
                }

                if(utils.isCD()) {
                    stage('Install Charts') {
                        landscaper.apply(manifestsDir, false)
                    }

                    stage('Save State') {
                        git.setUserInfo(outputGitUser, outputGitEmail)
                        git.addHostsToKnownHosts()
                        git.checkoutRepo(outputRepo, outputRepoBranch, outputRepoDir)
                        copyNewStateToOutputRepo(workspaceDir, outputRepoDir)
                        git.commitChanges(outputRepoDir, "Update landscaper state")   
                    }
                }
            } catch(e) {
                //TODO: Extract test result and send in notification
                slack.sendDefaultFailureNotification(slackWebHookURL, slackChannel, [slack.createErrorField(e)])
                
                def commentMessage = "[Build ${env.BUILD_NUMBER}](${env.BUILD_URL}) has Failed!"
                git.addCommentToPullRequest(commentMessage)

                throw e
            }

            stage('Notify') {
                def message
                if (utils.isCD()) {
                    message = "Deploy successful"
                }
                else {
                    message = "Dry Run successful"
                }
                slack.sendDefaultSuccessNotification(slackWebHookURL, slackChannel, [slack.createField("Message", message, false)])

                git.addCommentToPullRequest(message)
            }
        }
    }
}

def copyNewStateToOutputRepo(String inputRepoDir, String outputRepoDir) {
    sh """
        rm -rf ${outputRepoDir}/*
        cp -r ${inputRepoDir}/manifests/ ${outputRepoDir}/manifests/
    """
}
