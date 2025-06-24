# images/base/Dockerfile

FROM rockylinux/rockylinux:9.5

# Install common dependencies for Hadoop and Ozone in a single layer
RUN dnf install -y epel-release && \
    dnf install -y \
      dumb-init \
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

# Set common environment variables
ENV JAVA_HOME=/usr/lib/jvm/java-11
ENV TZ=Asia/Tokyo