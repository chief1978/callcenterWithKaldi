FROM centos:centos7
LABEL maintainer='Anton Stukach <anton.stukach@gmail.com>'
ENV build_date 2022-09-11

RUN yum -y update \
    && yum -y groupinstall core base "Development Tools" \
    && yum -y install \
        epel-release \
        espeak \
        dmidecode \
        gcc \
        gcc-c++ \
        ncurses-devel \
        libxml2-devel \
        make \
        wget \
        openssl-devel \
        newt-devel \
        kernel-devel \
        sqlite-devel \
        libuuid-devel \
        libevent-devel \
        libtermcap-devel \
        readline-devel \
        gtk2-devel \
        jansson-devel \
        binutils-devel \
        libedit \
        libedit-devel \
        lua \
        # lua-devel \
        lame \
        java \
        sox \
        unixODBC \
        unixODBC-devel \
        python3-pip \
    && yum clean all \
    && pip3 install pyst2 websocket-client \
    && cd /tmp \
    # Download and install cmake
    && git clone https://github.com/Kitware/CMake.git \
    && cd CMake && ./bootstrap && make && sudo make install \
    # Download and install mariaDB connector
    # && wget https://dlm.mariadb.com/2454028/Connectors/odbc/connector-odbc-3.1.17/mariadb-connector-odbc-3.1.17-centos74-amd64.tar.gz \
    # && tar zxpf mariadb-connector-odbc-3.1.17-centos74-amd64.tar.gz \
    # && cd mariadb-connector-odbc-3.1.17-centos74-amd64 \
    && git clone https://github.com/MariaDB/mariadb-connector-odbc.git \
    && cd mariadb-connector-odbc && git checkout 3.1.17 \
    && cd .. && mkdir build && cd build \
    && cmake ../mariadb-connector-odbc/ -DCMAKE_BUILD_TYPE=RelWithDebInfo -DCONC_WITH_UNIT_TESTS=Off -DCMAKE_INSTALL_PREFIX=/usr/local -DWITH_SSL=OPENSSL \
    && cmake --build . --config RelWithDebInfo \
    && make install \
    && cd /tmp \
    # Download and install luarock
    # && wget https://luarocks.org/releases/luarocks-3.8.0.tar.gz \
    # && tar zxpf luarocks-3.8.0.tar.gz \
    # && cd luarocks-3.8.0 \
    # && ./configure --with-lua-include=/usr/local/include \
    # && make \
    # && make install \
    # && cd /tmp \
    # Download asterisk
    && git clone -b 13.26 https://gerrit.asterisk.org/asterisk \
    && cd asterisk/contrib/scripts \
    && ./install_prereq install \
    && ./install_prereq install-unpackaged \
    && cd ../.. \
    && contrib/scripts/get_mp3_source.sh \
    # make asterisk
    # Configure
    && ./configure --libdir=/usr/lib64 1> /dev/null \
    # Remove the native build option add some options
    && make -j$(nproc) menuselect.makeopts \
    && menuselect/menuselect \
                          --disable BUILD_NATIVE \
                          --enable cdr_csv \
                          --enable chan_sip \
                          --enable res_snmp \
                          --enable res_http_websocket \
                          --enable format_mp3 \
                          --enable CORE-SOUNDS-RU-WAV \
                          --enable CORE-SOUNDS-RU-ULAW \
                          --enable CORE-SOUNDS-RU-ALAW \
                          --enable CORE-SOUNDS-RU-GSM \
                      menuselect.makeopts \
    # Continue with a standard make
    && make -j$(nproc) 1> /dev/null \
    && make -j$(nproc) install 1> /dev/null \
    && make -j$(nproc) install-logrotate 1> /dev/null \    
    # clean up the cached make files
    && make dist-clean \
    # Update max number of open files
    && sed -i -e 's/# MAXFILES=/MAXFILES=/' /usr/sbin/safe_asterisk \
    # Set tty
    && sed -i 's/TTY=9/TTY=/g' /usr/sbin/safe_asterisk \
    # Set se
    && sed -i 's/enforcing/permissive/' /etc/sysconfig/selinux \
    # && setenforce 0 \
    # Create and configure asterisk for running asterisk user
    && useradd -m asterisk -s /sbin/nologin \
    && chown -R asterisk:asterisk /var/run/asterisk \
                                  /etc/asterisk/ \
                                  /var/lib/asterisk \
                                  /var/log/asterisk \
                                  /var/spool/asterisk \
                                  /usr/lib64/asterisk/ \
    && cd /tmp \
    && git clone https://github.com/alphacep/vosk-asterisk.git \
    && cd vosk-asterisk \
    && ./bootstrap \
    && ./configure --with-asterisk=/usr --prefix=/usr \
    && make \
    && make install
# Running asterisk with user asterisk
USER asterisk
CMD /usr/sbin/asterisk -fvvvvv
