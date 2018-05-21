#!/usr/bin/groovy
@Library('github.com/stakater/fabric8-pipeline-library@master')

def utils = new io.fabric8.Utils()

toolsWithExternalKubeNode(toolsImage: 'stakater/pipeline-tools:1.6.0') {
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
            def common = new io.stakater.Common()

            try {
                stage('Init Helm') {
                    // Sleep is needed for the first time because tiller pod might not be ready instantly
                    helm.init(false)

                    sh "sleep 30s"

                    helm.addRepo("stakater", "https://stakater.github.io/stakater-charts")
                }

                stage('Dry Run Charts') {
                    landscaper.apply(manifestsDir, true)
                }

                if(utils.isCD()) {
                    stage('Install Charts') {
                        landscaper.apply(manifestsDir, false)
                    }

                    stage('Tag and Release') {
                        print "Generating New Version"
                        def versionFile = ".version"
                        def version = common.shOutput("jx-release-version --gh-owner=${repoOwner} --gh-repository=${repoName} --version-file ${versionFile}")
                        sh """
                            echo "${version}" > ${versionFile}
                        """
                        git.commitChanges(WORKSPACE, "Bump Version to ${version}")

                        print "Pushing Tag ${version} to Git"
                        git.createTagAndPush(WORKSPACE, version)
                        git.createRelease(version)
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
                def versionFile = ".version"
                def version = common.shOutput("cat ${versionFile}")
                if (utils.isCD()) {
                    message = "Release stakater-apps ${version}"
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
