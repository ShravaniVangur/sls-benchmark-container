# Use a base image with Python support
FROM fedora:latest

# Install prerequisites
RUN dnf -y update \
 && dnf -y install \
      python3 \
      python3-pip \
      git \
      autoconf \
      automake \
      make \
      gcc \
      m4 \
      pkg-config \
      glibc-devel \
      kernel-headers \
      kmod \
      wget \
      curl \
      procps-ng \
      dnf-utils \
      telnet \
      telnet-server \
      chkconfig \
      vsftpd \
      hostname \
      grep \
      numactl \
      && ln -sf /usr/bin/dnf /usr/bin/yum \
      && dnf clean all

RUN [ ! -e /usr/bin/python ] || rm -f /usr/bin/python && ln -s /usr/bin/python3 /usr/bin/python

RUN echo -e '#!/bin/bash\nexit 0' > /usr/bin/chkconfig && chmod +x /usr/bin/chkconfig


# Clone the SLS repository
COPY ./SLS_PYTHON /sls_python

# Set the working directory
WORKDIR /sls_python

RUN git clone https://github.com/linux-test-project/ltp.git /sls_python/ltp-master

# Run the installation script
RUN chmod +x ./install_sls.py ./ltp_testcase_updates.py

RUN rm -f /usr/bin/yum && \
    echo '#!/bin/bash' > /usr/bin/yum && \
    echo 'echo "Loaded plugins: fastestmirror"' >> /usr/bin/yum && \
    echo 'echo "No package matching xinetd available"' >> /usr/bin/yum && \
    echo 'exit 0' >> /usr/bin/yum && \
    chmod +x /usr/bin/yum

RUN echo -e '#!/bin/bash\nexit 0' > /usr/bin/systemctl && chmod +x /usr/bin/systemctl


RUN python -W ignore::SyntaxWarning ./install_sls.py

# Copy configuration files (if needed)
# ADD sls_config /opt/SLS_PYTHON/sls_config

# Expose necessary ports (if applicable)
EXPOSE 8080 8081

# Set the default command to start SLS
CMD ["python", "-W", "ignore::SyntaxWarning", "./start_sls.py", "-b"]
