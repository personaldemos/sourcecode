stage 'Build'
node {
    docker.image('cloudbees/java-build-tools:0.0.7.1').inside {
        git 'https://github.com/CloudBees-community/MoviePlex-OpenShift-Example.git'
        sh 'mvn clean package'
        archive 'target/*.war,target/*.jar'
    }
}

stage 'Deploy'
mail to: 'email.adress@domain.com', \
     subject: "${env.BUILD_TAG} ready to deploy", \
     body: """Deploy ${env.BUILD_TAG}?

Goto ${env.BUILD_URL}."""


input message: 'Deploy?', ok: 'Deploy'
node {
    docker.image('cloudbees/java-build-tools:0.0.7.1').inside {
        //Requires CloudBees OpenShift CLI Plugin to
        //automatically install OC client and log in to the server
        wrap([$class: 'OpenShiftBuildWrapper', 
                url: 'https://openshift.beesshop.org:8443',
                credentialsId: 'openshift-admin-aws',
                insecure: true, //Don't check server certificate
                ]) {
                // oc & source2image
                sh """
                oc project movieplex-application
                oc start-build j2ee-application-build
                """
        }
    }
}
