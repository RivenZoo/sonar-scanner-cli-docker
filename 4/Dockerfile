FROM debian:stretch-slim

ARG SONAR_SCANNER_HOME=/opt/sonar-scanner
ARG NODEJS_HOME=/opt/nodejs
ARG UID=1000
ARG GID=1000
ENV SONAR_SCANNER_HOME=${SONAR_SCANNER_HOME} \
    SONAR_SCANNER_VERSION=4.3.0.2102 \
    NODEJS_HOME=${NODEJS_HOME} \
    NODEJS_VERSION=v10.19.0 \
    PATH=${SONAR_SCANNER_HOME}/bin:${NODEJS_HOME}/bin:${PATH} \
    NODE_PATH=${NODEJS_HOME}/lib/node_modules

# 更新apt-get源
RUN echo \
    deb http://mirrors.aliyun.com/debian/ stretch main non-free contrib\
    deb-src http://mirrors.aliyun.com/debian/ stretch main non-free contrib\
    deb http://mirrors.aliyun.com/debian-security stretch/updates main\
    deb-src http://mirrors.aliyun.com/debian-security stretch/updates main\
    deb http://mirrors.aliyun.com/debian/ stretch-updates main non-free contrib\
    deb-src http://mirrors.aliyun.com/debian/ stretch-updates main non-free contrib\
    deb http://mirrors.aliyun.com/debian/ stretch-backports main non-free contrib\
    deb-src http://mirrors.aliyun.com/debian/ stretch-backports main non-free contrib\
    > /etc/apt/sources.list

RUN apt-get update \
    && apt-get install -y --no-install-recommends ca-certificates git wget unzip xz-utils pylint shellcheck locales locales-all \
    && rm -rf /var/lib/apt/lists/*

RUN locale-gen en_US.UTF-8  
ENV LANG en_US.UTF-8  
ENV LANGUAGE en_US:en  
ENV LC_ALL en_US.UTF-8  

RUN groupadd --gid ${GID} scanner-cli \
    && useradd --uid ${UID} --gid scanner-cli --shell /bin/bash --create-home scanner-cli

WORKDIR /opt
RUN wget -U "scannercli" -q -O /opt/sonar-scanner-cli.zip https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-${SONAR_SCANNER_VERSION}-linux.zip \
    && unzip sonar-scanner-cli.zip \
    && rm sonar-scanner-cli.zip \
    && mv sonar-scanner-${SONAR_SCANNER_VERSION}-linux ${SONAR_SCANNER_HOME} \
    && wget -U "nodejs" -q -O nodejs.tar.xz https://nodejs.org/dist/${NODEJS_VERSION}/node-${NODEJS_VERSION}-linux-x64.tar.xz \
    && tar Jxf nodejs.tar.xz \
    && rm nodejs.tar.xz \
    && mv node-${NODEJS_VERSION}-linux-x64 ${NODEJS_HOME} \
    && npm install -g typescript@3.6.3

COPY --chown=scanner-cli:scanner-cli bin /usr/bin/

WORKDIR /usr/src

USER scanner-cli

ENTRYPOINT ["/usr/bin/entrypoint.sh"]

CMD ["sonar-scanner"]
