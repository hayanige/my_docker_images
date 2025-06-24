# Base image is our Hadoop image. We will swap its Java version from 11 to 8.
ARG HADOOP_TAG=latest
FROM terrestrials/hadoop:${HADOOP_TAG}

USER root

RUN dnf install -y --setopt=tsflags=nodocs \
      java-1.8.0-openjdk \
      java-1.8.0-openjdk-devel \
    && dnf clean all \
    && rm -rf /var/cache/dnf

# Set JAVA_HOME to the newly installed Java 8
ENV JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk

# Build arguments for the tarball names, provided by the workflow
ARG HIVE_TARBALL
ARG TEZ_TARBALL

# Copy and extract Hive binary
COPY ${HIVE_TARBALL} /tmp/
RUN tar -xzf /tmp/${HIVE_TARBALL} -C /opt/ && \
    mv /opt/apache-hive-*-bin /opt/hive && \
    rm /tmp/${HIVE_TARBALL}

# Copy and extract Tez binary
COPY ${TEZ_TARBALL} /tmp/
RUN tar -xzf /tmp/${TEZ_TARBALL} -C /opt/ && \
    mv /opt/apache-tez-*-bin /opt/tez && \
    rm /tmp/${TEZ_TARBALL}

# Set Hive and Tez environment variables.
# The PATH is explicitly redefined to ensure it uses the new JAVA_HOME.
ENV PATH=${JAVA_HOME}/bin:/opt/hadoop/bin:/opt/hive/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
ENV TEZ_CONF_DIR=/opt/tez/conf \
    TEZ_JARS=/opt/tez \
    HADOOP_CLASSPATH=${TEZ_CONF_DIR}:${TEZ_JARS}/*:${TEZ_JARS}/lib/*

# Create hive user and group
RUN groupadd --gid 1005 hive && useradd hive --system --shell /sbin/nologin --uid 1005 --gid hive --groups hadoop && \
    echo "hive ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/hive && chmod 0440 /etc/sudoers.d/hive

# Create and set permissions for log directory
RUN mkdir -p /var/log/hive && chown hive:hive /var/log/hive && chmod 1775 /var/log/hive
ENV HIVE_LOG_DIR=/var/log/hive

# TZ (TimeZone) is already set in the base image.