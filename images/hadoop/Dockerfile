# Multi-stage build to extract only necessary Ozone JARs
ARG OZONE_TAG=latest
FROM terrestrials/ozone:${OZONE_TAG} AS ozone-jars

# Stage 2: Build Hadoop image using the common base
ARG BASE_IMAGE_TAG=latest
FROM terrestrials/base:${BASE_IMAGE_TAG}

# Copy and extract Hadoop tarball
ARG HADOOP_TARBALL=hadoop-*.tar.gz
COPY ${HADOOP_TARBALL} /tmp/

RUN tar -xzf /tmp/${HADOOP_TARBALL} -C /opt/ && \
    rm /tmp/${HADOOP_TARBALL} && \
    mv /opt/hadoop-* /opt/hadoop

ENV JAVA_HOME=/usr/lib/jvm/java-11 \
    PATH=/opt/hadoop/bin:${PATH}

# Set up users and directories
RUN groupadd --gid 1000 hadoop && useradd hadoop --system --shell /sbin/nologin --uid 1000 --gid hadoop && \
    groupadd --gid 1001 hdfs   && useradd hdfs   --system --shell /sbin/nologin --uid 1001 --gid hdfs --groups hadoop && \
    groupadd --gid 1002 yarn   && useradd yarn   --system --shell /sbin/nologin --uid 1002 --gid yarn --groups hadoop && \
    groupadd --gid 1003 mapred && useradd mapred --system --shell /sbin/nologin --uid 1003 --gid mapred --groups hadoop && \
    groupadd --gid 1004 httpfs && useradd httpfs --system --shell /sbin/nologin --uid 1004 --gid httpfs --groups hadoop

RUN echo "hadoop ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/hadoop && chmod 0440 /etc/sudoers.d/hadoop && \
    echo "hdfs   ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/hdfs   && chmod 0440 /etc/sudoers.d/hdfs   && \
    echo "yarn   ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/yarn   && chmod 0440 /etc/sudoers.d/yarn   && \
    echo "mapred ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/mapred && chmod 0440 /etc/sudoers.d/mapred && \
    echo "httpfs ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/httpfs && chmod 0440 /etc/sudoers.d/httpfs

RUN mkdir -p /var/log/hadoop && chmod 1777 /var/log/hadoop
ENV HADOOP_LOG_DIR=/var/log/hadoop

ENV TZ=Asia/Tokyo

# Copy only necessary Ozone JARs from the first stage
COPY --from=ozone-jars /opt/ozone/share/ozone/lib/ozone-filesystem-hadoop3-*.jar /opt/hadoop/share/hadoop/common/lib/
RUN find /opt/hadoop/share/hadoop/common/lib -name "ozone-filesystem-hadoop3-client-*.jar" -exec rm {} \;