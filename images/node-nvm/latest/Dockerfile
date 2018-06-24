FROM debian:jessie
MAINTAINER LethaK

# Prevent dialog during apt install
ENV DEBIAN_FRONTEND noninteractive

# Base packages
# -----------------------------------------------------------------------------
RUN apt-get update && \
    apt-get install -y apt-transport-https ca-certificates wget

# Adding packages.sury.org repository for PHP 7.2
RUN wget -O /etc/apt/trusted.gpg.d/php.gpg https://packages.sury.org/php/apt.gpg && \
    echo "deb https://packages.sury.org/php/ jessie main" > /etc/apt/sources.list.d/php.list


# PHP packages
# -----------------------------------------------------------------------------
RUN apt-get update && \
    apt-get install -y \
    bash-completion \
    wget \
    curl \
    sudo \
    git \
    cron \
    mysql-client \
    unzip \
    locales \
    nano ;


# Clear archives in apt cache folder
RUN apt-get clean && rm -rf /var/lib/apt/lists/*


ENTRYPOINT ["/bin/bash"]

ARG DOCKER_TIMEZONE=UTC
ARG DOCKER_LOCALE=fr_FR.UTF-8

# Configure timezone
# -----------------------------------------------------------------------------
RUN echo $DOCKER_TIMEZONE > /etc/timezone; dpkg-reconfigure -f noninteractive tzdata

# Configure locale for date, time & currency php functions
# -----------------------------------------------------------------------------
RUN dpkg-reconfigure locales && \
    echo $DOCKER_LOCALE' UTF-8'>> /etc/locale.gen && \
    locale-gen $DOCKER_LOCALE && \
    /usr/sbin/update-locale LANG=$DOCKER_LOCALE

ENV LC_ALL $DOCKER_LOCALE
ENV LANG $DOCKER_LOCALE
ENV LANGUAGE $DOCKER_LOCALE


# Local user
# -----------------------------------------------------------------------------
RUN mkdir /tmp/profile && \
    curl -s -o /tmp/profile/.bashrc_append https://raw.githubusercontent.com/lethak-docker/webplatform/master/build_files/node-nvm/profile/.bashrc_append && \
    curl -s -o /tmp/profile/.gitconfig https://raw.githubusercontent.com/lethak-docker/webplatform/master/build_files/node-nvm/profile/.gitconfig && \
    curl -s -o /tmp/profile/.vimrc_append https://raw.githubusercontent.com/lethak-docker/webplatform/master/build_files/node-nvm/profile/.vimrc_append

# nb: the 1000 used here for user id and group id is later on replaced by the code in bootstrap.sh...
RUN addgroup --gid 1000 site && \
    adduser --uid 1000 --ingroup site --home /home/site --shell /bin/bash --disabled-password --gecos "" site && \
    sed -i '$ a site   ALL=\(ALL:ALL\) NOPASSWD: ALL' /etc/sudoers

RUN curl -s -o /home/site/.git-completion.bash https://raw.githubusercontent.com/git/git/master/contrib/completion/git-completion.bash && \
    curl -s -o /home/site/.git-prompt.sh -OL https://github.com/git/git/raw/master/contrib/completion/git-prompt.sh && \
    cp /etc/skel/.* /home/site/; \
    cat /tmp/profile/.bashrc_append >> /home/site/.bashrc && \
    cat /tmp/profile/.vimrc_append >> /home/site/.vimrc && \
    cp /tmp/profile/.gitconfig /home/site/ && \
    mkdir -p /home/site/.composer && \
    mkdir -p /home/site/.ssh && \
    chown -R site:site /home/site/ && \
    find /tmp/ -type f -name '.*' -maxdepth 1  -delete

# Install fixuid (https://github.com/boxboat/fixuid) to map host user id with container 'site' user id (used in bootstrap.sh)
RUN USER=site && \
    GROUP=site && \
    curl -SsL https://github.com/boxboat/fixuid/releases/download/v0.1/fixuid-0.1-linux-amd64.tar.gz | tar -C /usr/local/bin -xzf - && \
    chown root:root /usr/local/bin/fixuid && \
    chmod 4755 /usr/local/bin/fixuid && \
    mkdir -p /etc/fixuid && \
    printf "user: $USER\ngroup: $GROUP\n" > /etc/fixuid/config.yml


# -----------------------------------------------------------------------------

# Clear archives in apt cache folder
RUN apt-get clean && rm -rf /var/lib/apt/lists/*

RUN curl -s -o /run/bootstrap.sh https://raw.githubusercontent.com/lethak-docker/webplatform/master/build_files/node-nvm/node_bootstrap.sh && \
    chmod 755 /run/bootstrap.sh

USER site:site
WORKDIR /var/www/

RUN curl -L -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash && \
export NVM_DIR="$HOME/.nvm" && \
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" && \
touch "$NVM_DIR/use" && \
nvm install 6 && \
nvm install 7 && \
nvm install 8 && \
nvm install 9 && \
nvm install 10 && \
nvm use 10 ;

CMD ["/run/bootstrap.sh"]
