void setBuildStatus(String message, String state) {
  step([
      $class: "GitHubCommitStatusSetter",
      reposSource: [$class: "ManuallyEnteredRepositorySource", url: "https://github.com/andrew-j-price/jenkins-pipeline"],
      contextSource: [$class: "ManuallyEnteredCommitContextSource", context: "ci/jenkins/build-status"],
      errorHandlers: [[$class: "ChangingBuildStatusErrorHandler", result: "UNSTABLE"]],
      statusResultSource: [ $class: "ConditionalStatusResultSource", results: [[$class: "AnyBuildResult", message: message, state: state]] ]
  ]);
}

pipeline {
    agent any

    stages {
        stage ('Notify') {
            steps {
                echo 'Pulling... ' + env.GIT_BRANCH
                echo "Running ${env.BUILD_TAG} on ${env.JENKINS_URL}"
                setBuildStatus("Build pending", "PENDING");
            }
        }
        stage ('Check') {
            steps {
                parallel (
                    unit: {
                        sh "echo 'unit tests...'"
                    },
                    env_variables: {
                        sh "env | sort -k1"
                        sh "whoami"
                    }
                )
            }
        }
        stage('Branch Logic') {
            steps {
                script {
                    if (env.GIT_BRANCH == 'origin/master') {
                        sh 'echo "On origin/master branch"'
                    } else {
                        sh 'echo "Not"'
                    }
                }
            }
        }
        stage ('Deploy') {
            steps {
                sh "echo 'Deploying...'"
            }
        }
    }

    post {
        success {
            setBuildStatus("Build complete", "SUCCESS");
        }
        failure {
            setBuildStatus("Build failed", "FAILURE");
        }
    }
}
