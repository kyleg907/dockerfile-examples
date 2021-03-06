# © Copyright IBM Corporation 2017, 2020
# LICENSE: Apache License, Version 2.0 (http://www.apache.org/licenses/LICENSE-2.0)

########## Dockerfile for Mariadb Server version 10.4.13 #########
#
# This Dockerfile builds a basic installation of Mariadb Server.
#
# MariaDB is a community-developed fork of the MySQL relational database management system intended to remain free under the GNU GPL.
# Being a fork of a leading open source software system, it is notable for being led by the original developers of MySQL, who forked it due to concerns over its acquisition by Oracle.
#
# To build this image, from the directory containing this Dockerfile
# (assuming that the file is named Dockerfile):
# docker build -t <image_name> .
#
# Use the following command to start mariadb server.
# docker run --name <container name> -p <port>:3306 -d <image name>
#
# Provide custom configuration file
# docker run --name <container_name> -v <host>/my.cnf:/etc/mysql/my.cnf -d -p <port>:3306 <image_name>
#
######################################################################################

# Base Image
FROM s390x/ubuntu:18.04

ARG MARIADB_VER=10.4.13

# The author
LABEL maintainer="LoZ Open Source Ecosystem (https://www.ibm.com/developerworks/community/groups/community/lozopensource)"

# Set environment variable
ENV SOURCE_DIR=/tmp/source
WORKDIR $SOURCE_DIR

###Install build dependencies
RUN apt-get update && apt-get install -y \
    git \
    bison \
    make \
    wget \
    tar \
    cmake \
    gcc \
    g++ \
    libssl-dev \
    libncurses-dev \
    libboost-dev \
    libboost-program-options-dev \
    check \
    openssl \
    libpcre3-dev \
    libpam0g-dev \
##Get MariaDB code from Git
 && cd $SOURCE_DIR && git clone https://github.com/MariaDB/server.git \
 && cd server \
 && git checkout mariadb-${MARIADB_VER} \
 && git submodule update --init --recursive \
##Build, configure, install
 && mkdir $SOURCE_DIR/build_mariadb \
 && cd $SOURCE_DIR/build_mariadb \
 && cmake $SOURCE_DIR/server \
 && make \
 && make install \
 && groupadd -r mysql && useradd -r -g mysql mysql \
 && cd /usr/local/mysql \
 && chown -R mysql .  \
 && chmod -R o+rwx . \
 && scripts/mysql_install_db --user=mysql \
 && sleep 20s \
 && cp support-files/mysql.server /etc/init.d/mysql \
# Cleanup
 && apt-get remove -y \
    git       \
    bison     \
    make      \
    wget      \
    cmake     \
    libssl-dev \
    libncurses-dev \
    libboost-dev \
    libboost-program-options-dev \
    libpcre3-dev \
    libpam0g-dev \
 && apt autoremove -y \
 && apt-get clean && rm -rf /var/lib/apt/lists/* \
 && rm -rf $SOURCE_DIR/server $SOURCE_DIR/build_mariadb

# Expose MariaDB default port
EXPOSE 3306

VOLUME /usr/local/mysql/data

ENV PATH=$PATH:/usr/local/mysql/bin
# Start MariaDB Server
CMD mysqld --user=mysql

# End of Dockerfile
