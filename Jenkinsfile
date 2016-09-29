#!/usr/bin/env groovy
node('vagrant') {

    currentBuild.result = "SUCCESS"

    try {

       stage 'Checkout'

            checkout scm
            echo "$env.GEM_ROOT"
            sh """
                eval "$(chef shell-init bash)"
                #bundle install --path .bundle/gems
            """"
            echo "$env.GEM_ROOT"

       stage 'Check_style'

            sh """
                eval "$(chef shell-init bash)"
                bundle exec rake style
            """

/*
       stage 'Unittest'

           sh 'rake unit'

       stage 'TestKitchen_integration'

            sh 'rake integration'
*/

       stage 'Cleanup'

            echo 'Cleanup'
            sh 'rm node_modules -rf'

            mail body: 'project build successful',
                        from: 'eosplus-dev+jenkins@arista',
                        replyTo: 'eosplus-dev@arista',
                        subject: 'project build successful',
                        to: 'jere@arista.com'

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

