# Build Ozone image using the common base
ARG BASE_IMAGE_TAG=latest
FROM terrestrials/base:${BASE_IMAGE_TAG}

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

# Copy and extract Ozone tarball provided as a build argument
ARG OZONE_TARBALL
COPY ${OZONE_TARBALL} /tmp/

RUN tar -xzf /tmp/${OZONE_TARBALL} -C /opt/ && \
    rm /tmp/${OZONE_TARBALL} && \
    mv /opt/ozone-* /opt/ozone && \
    chown -R ozone:ozone /opt/ozone

# Add Ozone to PATH after extraction
ENV PATH=/opt/ozone/bin:${PATH}

USER ozone