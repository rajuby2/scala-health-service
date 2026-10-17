pipeline {
    agent any

    environment {
        QUAY_REPO   = 'quay.io/rajuby2/scala-health-service'
        IMAGE_TAG   = "${env.GIT_COMMIT.take(7)}-${env.BUILD_NUMBER}"
        OC_SERVER   = 'https://api.rm3.7wse.p1.openshiftapps.com:6443' // update to your sandbox API server
        OC_NAMESPACE = 'rajuby2-dev'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Test') {
            steps {
                sh './gradlew --no-daemon test'
            }
        }

        stage('Build & Push Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'quay-io-creds', usernameVariable: 'QUAY_USER', passwordVariable: 'QUAY_PASS')]) {
                    sh """
                        docker build -t ${QUAY_REPO}:${IMAGE_TAG} .
                        echo "\$QUAY_PASS" | docker login quay.io -u "\$QUAY_USER" --password-stdin
                        docker push ${QUAY_REPO}:${IMAGE_TAG}
                    """
                }
            }
        }

        stage('Deploy to OpenShift') {
            steps {
                withCredentials([string(credentialsId: 'openshift-token', variable: 'OC_TOKEN')]) {
                    sh """
                        oc login --token=\$OC_TOKEN --server=${OC_SERVER}
                        oc project ${OC_NAMESPACE}
                        oc apply -f openshift/
                        oc set image deployment/scala-health-service scala-health-service=${QUAY_REPO}:${IMAGE_TAG}
                        oc rollout status deployment/scala-health-service
                    """
                }
            }
        }
    }

    post {
        always {
            sh 'docker logout quay.io || true'
        }
    }
}
