# Update the VARIANT arg in devcontainer.json to pick a Python version: 3, 3.8, 3.7, 3.6 
ARG VARIANT=3.8.3-slim-buster
ARG PULUMI_VERSION="2.7.1"
 
FROM python:${VARIANT}
 
# This Dockerfile adds a non-root user with sudo access. Use the "remoteUser"
# property in devcontainer.json to use it. On Linux, the container user's GID/UIDs
# will be updated to match your local UID/GID (when using the dockerFile property).
# See https://aka.ms/vscode-remote/containers/non-root-user for details.
ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=$USER_UID
 
# Default set of utilities to install in a side virtual env
ARG DEFAULT_UTILS="\
 flake8 \
 flake8-cognitive-complexity \
 flake8-eradicate \
 flake8-use-fstring \
 flake8-bandit \
 coverage \
 black \
 pytest \
 pytest-black\
 bandit \
 virtualenv"
 
ENV PIPX_HOME=/usr/local/py-utils
ENV PIPX_BIN_DIR=${PIPX_HOME}/bin
ENV PATH=${PATH}:${PIPX_BIN_DIR}
 
# Options for common package install script
ARG INSTALL_ZSH="true"
ARG UPGRADE_PACKAGES="true"
ARG COMMON_SCRIPT_SOURCE="https://raw.githubusercontent.com/microsoft/vscode-dev-containers/master/script-library/common-debian.sh"
ARG COMMON_SCRIPT_SHA="dev-mode"
 
# Configure apt and install packages
RUN apt-get update \
 && export DEBIAN_FRONTEND=noninteractive \
 # Verify git, common tools / libs installed, add/modify non-root user, optionally install zsh
 && apt-get -y install --no-install-recommends rsync curl vim ca-certificates 2>&1 \
 && curl -sSL ${COMMON_SCRIPT_SOURCE} -o /tmp/common-setup.sh \
 && ([ "${COMMON_SCRIPT_SHA}" = "dev-mode" ] || (echo "${COMMON_SCRIPT_SHA} */tmp/common-setup.sh" | sha256sum -c -)) \
 && /bin/bash /tmp/common-setup.sh "${INSTALL_ZSH}" "${USERNAME}" "${USER_UID}" "${USER_GID}" "${UPGRADE_PACKAGES}" \
 && rm /tmp/common-setup.sh \
 && rm -rf /var/lib/apt/lists/*
 
RUN echo "${DEFAULT_UTILS}" | xargs -n 1 pip3 install \
 # Tactically remove imagemagick due to https://security-tracker.debian.org/tracker/CVE-2019-10131
 # Clean up
 && apt-get autoremove -y \
 && apt-get clean -y \
 && rm -rf /var/lib/apt/lists/*
 
#Install Azure CLI
RUN curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
 
# Install the Pulumi SDK, including the CLI and language runtimes with specified version
ARG PULUMI_VERSION="2.7.1"
RUN curl -fsSL https://get.pulumi.com/ | bash -s -- --version $(echo $PULUMI_VERSION); \
 mv ~/.pulumi/bin/* /usr/bin
