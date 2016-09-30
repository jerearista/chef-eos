#!/usr/bin/env groovy
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

            sh """
                eval "\$(chef shell-init bash)"
                rake style || true
            """
        }

/*
        stage ('Warnings rubocop') {
            step([$class: 'WarningsPublisher', canComputeNew: false, canResolveRelativePaths: false, defaultEncoding: '', excludePattern: '', healthy: '', includePattern: '', messagesPattern: '', parserConfigurations: [[parserName: 'Rubocop', pattern: 'error_and_warnings.txt']], unHealthy: ''])
        }
*/

        // step([$class: 'hudson.plugins.checkstyle.CheckStylePublisher', checkstyle: ''])

        stage ('Unittest') {

            sh """
                eval "\$(chef shell-init bash)"
                gem list --local
                gem install foodcritic
                gem install bundler
                bundle install
                rake unit || true
            """

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

