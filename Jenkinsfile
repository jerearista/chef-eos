#!/usr/bin/env groovy
node('node') {

    currentBuild.result = "SUCCESS"

    try {

       stage 'Checkout'

            checkout scm

       stage 'Check_style'

           sh 'rake style'

       #stage 'Unittest'

#           sh 'rake unit'

#       stage 'TestKitchen_integration'
#
#            sh 'rake integration'

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

