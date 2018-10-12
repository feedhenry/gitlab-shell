#!groovy

// https://github.com/feedhenry/fh-pipeline-library
@Library('fh-pipeline-library') _

String COMPONENT = 'gitlab-shell'
String VERSION = ""
String BUILD = ""
String DOCKER_HUB_ORG = "rhmap"
String DOCKER_HUB_REPO = COMPONENT
String CHANGE_URL = ""

stage('Trust') {
    enforceTrustedApproval()
}

fhBuildNode(['label': 'openshift']) {

    VERSION = readFile("VERSION").trim()
    BUILD = env.BUILD_NUMBER
    CHANGE_URL = env.CHANGE_URL

    stage('Platform Update') {
        final Map updateParams = [
                componentName: COMPONENT,
                componentVersion: VERSION,
                componentBuild: BUILD,
                changeUrl: CHANGE_URL
        ]
        fhCoreOpenshiftTemplatesComponentUpdate(updateParams)
    }

    stash COMPONENT
    archiveArtifacts writeBuildInfo(COMPONENT, "${VERSION}-${BUILD}")
}

node('master') {
    stage('Build Image') {
        unstash COMPONENT

        final Map params = [
                fromDir: '.',
                buildConfigName: COMPONENT,
                imageRepoSecret: "dockerhub",
                outputImage: "docker.io/${DOCKER_HUB_ORG}/${DOCKER_HUB_REPO}:${VERSION}-${BUILD}"
        ]

        try {
            buildWithDockerStrategy params
        } finally {
            sh "rm -rf *"
        }
    }
}
