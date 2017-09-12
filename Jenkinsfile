#!groovy

// https://github.com/feedhenry/fh-pipeline-library
@Library('fh-pipeline-library') _

final String COMPONENT = 'gitlab-shell'
final String VERSION = readFile("VERSION.txt").trim()
final String DOCKER_HUB_ORG = "rhmap"
final String DOCKER_HUB_REPO = COMPONENT

fhBuildNode(['label': 'openshift']) {

    stage('Build Image') {
        dockerBinaryBuild(COMPONENT, VERSION, DOCKER_HUB_ORG, DOCKER_HUB_REPO, 'dockerhubjenkins', '.')
    }

}
