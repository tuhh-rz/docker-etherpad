# Stable version of etherpad doesn't support npm 2
FROM ubuntu:18.04

ARG VERSION=${VERSION}
ENV VERSION=$VERSION
RUN echo $VERSION

ARG DOWNLOAD=${DOWNLOAD}
ENV DOWNLOAD=$DOWNLOAD
RUN echo $DOWNLOAD

# Let the conatiner know that there is no tty
ENV DEBIAN_FRONTEND noninteractive

RUN apt-get update && \
    apt-get upgrade -qy

RUN apt-get install -y \
    build-essential \
    curl \
    git \
    gzip \
    libssl-dev \
    pkg-config \
    python

RUN apt-get install -y \
    mysql-client

RUN rm -r /var/lib/apt/lists/*

# 9.x is DEPRECATED!!!!
RUN curl -sL https://deb.nodesource.com/setup_10.x | bash -
RUN apt-get install -y nodejs




# plugins to install while building the container. By default no plugins are
# installed.
# If given a value, it has to be a space-separated, quoted list of plugin names.
#
# EXAMPLE:
#   ETHERPAD_PLUGINS="ep_codepad ep_author_neat"
# ARG ETHERPAD_PLUGINS="ep_adminpads ep_page_view ep_headings ep_markdown ep_mediawiki"
ARG ETHERPAD_PLUGINS="ep_adminpads ep_headings ep_markdown ep_mediawiki"

# Set the following to production to avoid installing devDeps
# this can be done with build args (and is mandatory to build ARM version)
ENV NODE_ENV=production

# Follow the principle of least privilege: run as unprivileged user.
#
# Running as non-root enables running this image in platforms like OpenShift
# that do not allow images running as root.
RUN useradd --uid 5001 --create-home etherpad

WORKDIR /opt/

RUN git clone -b ${VERSION} https://github.com/ether/etherpad-lite.git

WORKDIR /opt/etherpad-lite

#  Anpassungen kopieren
COPY ./src/templates/* ./src/templates/

RUN chown -R etherpad:etherpad /opt/etherpad-lite

USER etherpad:etherpad

# install node dependencies for Etherpad
RUN bin/installDeps.sh && \
    rm -rf ~/.npm/_cacache

# Install the plugins, if ETHERPAD_PLUGINS is not empty.
#
# Bash trick: in the for loop ${ETHERPAD_PLUGINS} is NOT quoted, in order to be
# able to split at spaces.
RUN for PLUGIN_NAME in ${ETHERPAD_PLUGINS}; do npm install "${PLUGIN_NAME}"; done

# Copy the configuration file.
RUN cp ./settings.json.docker settings.json

EXPOSE 9001
CMD ["node", "node_modules/ep_etherpad-lite/node/server.js"]

LABEL \
    de.tuhh.ci_repository_url=$CI_REPOSITORY_URL \
    de.tuhh.ci_commit_short_sha=$CI_COMMIT_SHORT_SHA \
    de.tuhh.build-date=$BUILD_DATE \
    de.tuhh.version=$VERSION