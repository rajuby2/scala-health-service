FROM jenkins/jenkins:lts-jdk21

USER root

# podman-docker provides a "docker" shim around podman, so the Jenkinsfile's
# "docker build/push" lines work unchanged against the host's podman socket.
RUN apt-get update \
    && apt-get install -y --no-install-recommends podman podman-docker curl \
    && rm -rf /var/lib/apt/lists/*

# OpenShift CLI (used by the pipeline's "oc" deploy steps)
RUN curl -sL https://mirror.openshift.com/pub/openshift-v4/clients/ocp/stable/openshift-client-linux.tar.gz \
    | tar -xz -C /usr/local/bin oc kubectl

ENV DOCKER_HOST=unix:///run/podman/podman.sock
ENV CONTAINER_HOST=unix:///run/podman/podman.sock

# Stay root: the mounted podman.sock is owned by root inside the podman
# machine VM, and this is a local dev Jenkins, not a multi-tenant one.
# Skip the setup wizard prompts; plugins are still installed/managed via the UI
ENV JAVA_OPTS="-Djenkins.install.runSetupWizard=true"
