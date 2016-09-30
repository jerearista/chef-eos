#!/usr/bin/env groovy

import groovy.json.JsonOutput
// Add whichever params you think you'd most want to have
// replace the slackURL below with the hook url provided by
// slack when you configure the webhook
def notifySlack(text, channel) {
    def slackURL = 'https://hooks.slack.com/services/xxxxxxx/yyyyyyyy/zzzzzzzzzz'
    def payload = JsonOutput.toJson([text      : text,
                                     channel   : channel,
                                     username  : "jenkins",
                                     icon_emoji: ":jenkins:"])
    sh "curl -X POST --data-urlencode \'payload=${payload}\' ${slackURL}"
}

node('vagrant') {

    currentBuild.result = "SUCCESS"

    try {

        stage ('Checkout') {

            checkout scm
            echo "$env.GEM_ROOT"
            sh """
                eval "\$(chef shell-init bash)"
                #bundle install --path .bundle/gems
            """
            echo "$env.GEM_ROOT"
        }

        stage ('Check_style') {

            try {
                sh """
                    eval "\$(chef shell-init bash)"
                    rake style
                """
            }
            catch (Exception err) {
                currentBuild.result = "UNSTABLE"
            }
            echo "RESULT: ${currentBuild.result}"

        }

/*
        stage ('Warnings rubocop') {
            step([$class: 'WarningsPublisher', canComputeNew: false, canResolveRelativePaths: false, defaultEncoding: '', excludePattern: '', healthy: '', includePattern: '', messagesPattern: '', parserConfigurations: [[parserName: 'Rubocop', pattern: 'error_and_warnings.txt']], unHealthy: ''])
        }
*/

        // step([$class: 'hudson.plugins.checkstyle.CheckStylePublisher', checkstyle: ''])

        stage ('ChefSpec Unittest') {

            sh """
                eval "\$(chef shell-init bash)"
                gem list --local
                gem install foodcritic
                gem install bundler
                bundle install
                rake unit
            """
            step([$class: 'JUnitResultArchiver', testResults: '**/coverage/*.json'])
            publishHTML(target: [reportDir:'coverage', reportFiles: 'index.html', reportName: 'ChefSpec Unittest Coverage'])

/*
        step([
            $class: 'RcovPublisher',
            reportDir: "coverage",
            targets: [
                [metric: "CODE_COVERAGE", healthy: 75, unhealthy: 50, unstable: 30]
            ]
        ])
*/

/*
        stage ('TestKitchen_integration') {

            sh 'rake integration'
        }
*/
        }

        stage ('Cleanup') {

            echo 'Cleanup'
            // @LogParserPublisher

            mail body: 'project build successful',
                        from: 'eosplus-dev+jenkins@arista',
                        replyTo: 'eosplus-dev@arista',
                        subject: 'project build successful',
                        to: 'jere@arista.com'

        }

    }

    catch (err) {

        currentBuild.result = "FAILURE"

            mail body: "project build error is here: ${env.BUILD_URL}" ,
            from: 'eosplus-dev+jenkins@arista.com',
            replyTo: 'eosplus-dev+jenkins@arista.com',
            subject: 'project build failed',
            to: 'jere@arista.com'

            throw err
    }

}

