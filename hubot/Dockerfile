FROM ubuntu:trusty-20170330
MAINTAINER "Ryan McCaul"

EXPOSE 6379

ENV DIRECTORY "/home/hubot"
ENV NAME "hubot"
ENV OWNER "Ryan McCaul"
ENV DESCRIPTION "Hubot"
ENV NODE_VERSION "7.7.3"

RUN apt-get update && \
    apt-get install -y sudo \
                       autoconf \
                       build-essential \
                       ca-certificates \
                       curl \
                       git-core \
                       redis-server

RUN useradd -d "$DIRECTORY" -ms /bin/bash hubot
RUN echo "hubot ALL=(ALL:ALL) NOPASSWD:ALL" >> /etc/sudoers
WORKDIR "$DIRECTORY"
USER hubot

# DOWNLOAD NODENV AND PATH
RUN git clone git://github.com/OiNutter/nodenv.git /home/hubot/.nodenv && \
    git clone git://github.com/OiNutter/node-build.git /home/hubot/.nodenv/plugins/node-build
ENV PATH /home/hubot/.nodenv/shims:/home/hubot/.nodenv/bin:/home/hubot/.nodenv/versions/$NODE_VERSION/bin:$PATH

# INSTALL NODENV VERSION
RUN nodenv install "$NODE_VERSION"
RUN nodenv global "$NODE_VERSION"
RUN nodenv rehash

# CREATE HUBOT BASE
RUN npm config set unsafe-perm true
RUN npm cache clean && npm install -g yo coffee-script
ADD ./conf/ "$DIRECTORY"
RUN npm install generator-hubot
RUN npm install hubot-cat --save
RUN npm install --save hubot-standup-alarm

# INSTALL APP
RUN yo hubot --owner="$OWNER" --name="$NAME" --description="DESCRIPTION" --defaults

# STARTING APP AND SERVICES
RUN echo "sudo /usr/bin/redis-server /etc/redis/redis.conf ; /home/hubot/bin/hubot --adapter slack > /home/hubot/hubot.log 2>&1 &" > /home/hubot/init_app.sh
RUN chmod +x "$DIRECTORY"/init_app.sh

# START EVERYTHING AND WATCHING LOGS
CMD bash /home/hubot/init_app.sh && tail -f /home/hubot/hubot.log
