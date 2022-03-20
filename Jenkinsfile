podTemplate(containers: [
    containerTemplate(name: "docker-build", image: "gcr.io/kaniko-project/executor:debug", command: "sleep", args: "99999")
]) {
    node(POD_LABEL) {
        def repo = checkout scm

        container("docker-build") {
            withCredentials([
                usernamePassword(
                    credentialsId: "registry",
                    usernameVariable: "REGISTRY_USER",
                    passwordVariable: "REGISTRY_PASSWORD"
                )
            ]) {
                sh 'echo "{\\"auths\\": {\\"registry.bcetha.de\\": {\\"username\\": \\"$REGISTRY_USER\\", \\"password\\": \\"$REGISTRY_PASSWORD\\"}}}" > /kaniko/.docker/config.json'
            }

            sh """
            /kaniko/executor --force --context . --dockerfile ./Dockerfile \
            --snapshotMode=time \
            --destination registry.bcetha.de/network/homepage:latest \
            --destination registry.bcetha.de/network/homepage:${repo.GIT_COMMIT} \
            --destination registry.bcetha.de/network/homepage:${repo.GIT_BRANCH} \
            --label org.opencontainers.image.source=https://git.bcetha.de/network/homepage \
            --label org.opencontainers.image.title=homepage \
            --label org.opencontainers.image.revision=${repo.GIT_COMMIT}
            """
        }
    }
}
