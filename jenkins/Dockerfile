FROM jenkins/jenkins:lts-jdk21

USER root

# Docker CLI (used by the pipeline's "docker build/push" steps; the daemon
# itself runs on the host and is reached via the mounted socket)
RUN apt-get update \
    && apt-get install -y --no-install-recommends docker.io curl \
    && rm -rf /var/lib/apt/lists/*

# OpenShift CLI (used by the pipeline's "oc" deploy steps)
RUN curl -sL https://mirror.openshift.com/pub/openshift-v4/clients/ocp/stable/openshift-client-linux.tar.gz \
    | tar -xz -C /usr/local/bin oc kubectl

# Let the jenkins user talk to the docker socket mounted from the host
RUN groupadd -f docker && usermod -aG docker jenkins

USER jenkins

# Skip the setup wizard prompts; plugins are still installed/managed via the UI
ENV JAVA_OPTS="-Djenkins.install.runSetupWizard=true"
