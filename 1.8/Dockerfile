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

RUN rm -r /var/lib/apt/lists/*

# 9.x is DEPRECATED!!!!
RUN curl -sL https://deb.nodesource.com/setup_10.x | bash -
RUN apt-get install -y nodejs

WORKDIR /opt/

RUN git clone -b ${VERSION} https://github.com/ether/etherpad-lite.git && cd etherpad-lite

WORKDIR /opt/etherpad-lite

# RUN bin/installDeps.sh && rm settings.json
# RUN rm settings.json

RUN apt-get install -y \
    mysql-client

COPY entrypoint.sh /entrypoint.sh
RUN chmod +x "/entrypoint.sh"

RUN npm install ep_adminpads
RUN npm install ep_page_view
RUN npm install ep_headings
RUN npm install ep_markdown
RUN npm install ep_mediawiki

# RUN npm install ep_user_pad_frontend

RUN sed -i 's/^node/exec\ node/' bin/run.sh

# VOLUME /opt/etherpad-lite/var
# RUN ln -s var/settings.json settings.json

EXPOSE 9001
ENTRYPOINT ["/entrypoint.sh"]
CMD ["bin/run.sh", "--root"]
