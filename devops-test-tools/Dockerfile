ARG DEBIAN_RELEASE=bookworm
FROM debian:${DEBIAN_RELEASE}-slim
ARG DEBIAN_FRONTEND=noninteractive
ARG INSTALL_DIR=/tmp/install
ARG KUBECONFORM_VERSION=0.6.4
ARG HADOLINT_VERSION=2.12.0
# hadolint ignore=DL3008
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

RUN mkdir ${INSTALL_DIR}
WORKDIR ${INSTALL_DIR}

RUN apt-get update \
&& apt-get install --yes --no-install-recommends ca-certificates apt-transport-https yq jq curl wget gpg git \
&& rm -rf /var/lib/apt/lists/* \
&& wget --progress=dot:giga -O get_helm.sh "https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3" \
&& chmod 700 get_helm.sh \
&& ./get_helm.sh \
&& helm plugin install --version "v0.4.2" https://github.com/helm-unittest/helm-unittest.git \
&& ARCHIVE="kubeconform-linux-amd64" \
&& mkdir "/opt/${ARCHIVE}" \
&& wget --progress=dot:giga -O "$ARCHIVE.tar.gz" "https://github.com/yannh/kubeconform/releases/download/v${KUBECONFORM_VERSION}/${ARCHIVE}.tar.gz" \
&& wget --progress=dot:giga -O "$ARCHIVE.sha256sum" "https://github.com/yannh/kubeconform/releases/download/v${KUBECONFORM_VERSION}/CHECKSUMS" \
&& sha256sum --check --ignore-missing "${ARCHIVE}.sha256sum" \
&& tar -xzf "${ARCHIVE}.tar.gz" -C "/opt/${ARCHIVE}" \ 
&& rm -f "${ARCHIVE}.tar.gz" \
&& ln -s "/opt/${ARCHIVE}/kubeconform" /usr/local/bin/kubeconform \
&& ARCHIVE="hadolint-Linux-x86_64" \
&& mkdir "/opt/${ARCHIVE}" \
&& wget --progress=dot:giga "https://github.com/hadolint/hadolint/releases/download/v${HADOLINT_VERSION}/${ARCHIVE}" \
&& wget --progress=dot:giga -O "${ARCHIVE}.sha256" "https://github.com/hadolint/hadolint/releases/download/v${HADOLINT_VERSION}/${ARCHIVE}.sha256" \
&& sha256sum --check --ignore-missing "${ARCHIVE}.sha256" \
&& mv "${ARCHIVE}" "/opt/${ARCHIVE}/hadolint" \
&& chmod a+x "/opt/${ARCHIVE}/hadolint" \
&& ln -s "/opt/${ARCHIVE}/hadolint" /usr/local/bin/hadolint \
&& kubeconform -v \
&& helm version \
&& hadolint -v

#ENTRYPOINT [ "/bin/bash" ]
#CMD [ "helm version" ]
