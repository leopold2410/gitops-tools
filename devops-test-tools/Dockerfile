ARG DEBIAN_RELEASE=bookworm
FROM debian:${DEBIAN_RELEASE}-slim
ARG DEBIAN_FRONTEND=noninteractive
ARG INSTALL_DIR=/tmp/install
ARG KUBECONFORM_VERSION=0.6.4
ARG HADOLINT_VERSION=2.12.0

SHELL ["/bin/bash", "-o", "pipefail", "-c"]

RUN mkdir ${INSTALL_DIR}
WORKDIR ${INSTALL_DIR}

RUN apt-get update 
RUN apt-get install --yes --no-install-recommends ca-certificates apt-transport-https yq jq curl wget gpg git

RUN curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 \
&& chmod 700 get_helm.sh \
&& ./get_helm.sh

RUN ARCHIVE="kubeconform-linux-amd64" \
&& mkdir /opt/${ARCHIVE} \
&& wget -O $ARCHIVE.tar.gz "https://github.com/yannh/kubeconform/releases/download/v${KUBECONFORM_VERSION}/${ARCHIVE}.tar.gz" \
&& wget -O $ARCHIVE.sha256sum "https://github.com/yannh/kubeconform/releases/download/v${KUBECONFORM_VERSION}/CHECKSUMS" \
&& sha256sum --check --ignore-missing ${ARCHIVE}.sha256sum \
&& tar -xzf ${ARCHIVE}.tar.gz -C /opt/${ARCHIVE} \ 
&& rm -f ${ARCHIVE}.tar.gz \
&& ln -s /opt/${ARCHIVE}/kubeconform /usr/local/bin/kubeconform

RUN ARCHIVE="hadolint-Linux-x86_64" \
&& mkdir /opt/${ARCHIVE} \
&& wget https://github.com/hadolint/hadolint/releases/download/v${HADOLINT_VERSION}/${ARCHIVE} \
&& wget -O ${ARCHIVE}.sha256 https://github.com/hadolint/hadolint/releases/download/v${HADOLINT_VERSION}/${ARCHIVE}.sha256 \
&& sha256sum --check --ignore-missing ${ARCHIVE}.sha256 \
&& mv ${ARCHIVE} /opt/${ARCHIVE}/hadolint \
&& chmod a+x /opt/${ARCHIVE}/hadolint \
&& ln -s /opt/${ARCHIVE}/hadolint /usr/local/bin/hadolint

RUN kubeconform -v
RUN helm version
RUN hadolint -v

#ENTRYPOINT [ "/bin/bash" ]
#CMD [ "helm version" ]
