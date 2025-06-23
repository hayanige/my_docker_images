# Runtime-only Dockerfile (assumes pre-built Ozone tarball)
FROM rockylinux/rockylinux:9.5

# Install dependencies
RUN dnf install -y epel-release && \
    dnf install -y dumb-init && \
    dnf install -y \
      krb5-workstation \
      java-11-openjdk \
      java-11-openjdk-devel \
      iputils \
      net-tools \
      bind-utils \
      nc \
      sudo \
      procps-ng \
      ncurses \
      tree \
      jq \
      python3-pip && \
    dnf clean all

RUN pip3 install awscli

# Set up users and directories
RUN groupadd --gid 2000 ozone && \
    useradd ozone --system --shell /usr/sbin/nologin --uid 2000 --gid ozone && \
    echo "ozone ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/ozone && \
    chmod 0440 /etc/sudoers.d/ozone

RUN \
    useradd alice --uid 50000 --gid 100 && \
    useradd bob --uid 50001 --gid 100 && \
    useradd nick --uid 50002 --gid 100

RUN mkdir -p /var/log/ozone && chmod 1777 /var/log/ozone

# Environment variables
ENV JAVA_HOME=/usr/lib/jvm/java-11 \
    OZONE_LOG_DIR=/var/log/ozone \
    TZ=Asia/Tokyo

# Copy and extract Ozone tarball
ARG OZONE_TARBALL=ozone-*.tar.gz
COPY ${OZONE_TARBALL} /tmp/

RUN tar -xzf /tmp/${OZONE_TARBALL} -C /opt/ && \
    rm /tmp/${OZONE_TARBALL} && \
    mv /opt/ozone-* /opt/ozone && \
    chown -R ozone:ozone /opt/ozone

# Add Ozone to PATH after extraction
ENV PATH=/opt/ozone/bin:${PATH}

USER ozone