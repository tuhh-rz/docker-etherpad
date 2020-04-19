# Stable version of etherpad doesn't support npm 2
#FROM debian:jessie
FROM ubuntu:16.04

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

RUN apt-get install -y curl unzip nodejs-legacy npm mysql-client node-pg postgresql-client && \
    rm -r /var/lib/apt/lists/*

WORKDIR /opt/

RUN curl -SL \
    https://github.com/ether/etherpad-lite/archive/${VERSION}.zip \
    > etherpad.zip && unzip etherpad && rm etherpad.zip && \
    mv etherpad-lite-${VERSION} etherpad-lite

COPY ./src/templates/* /opt/etherpad-lite/src/templates/

WORKDIR etherpad-lite

RUN bin/installDeps.sh && rm settings.json
COPY entrypoint.sh /entrypoint.sh
RUN chmod +x "/entrypoint.sh"

RUN npm install ep_adminpads
RUN npm install ep_page_view
RUN npm install ep_headings
RUN npm install ep_markdown
RUN npm install ep_mediawiki

# RUN npm install ep_user_pad_frontend

RUN sed -i 's/^node/exec\ node/' bin/run.sh

VOLUME /opt/etherpad-lite/var
RUN ln -s var/settings.json settings.json

EXPOSE 9001
ENTRYPOINT ["/entrypoint.sh"]
CMD ["bin/run.sh", "--root"]
