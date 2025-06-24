# images/base/Dockerfile

FROM rockylinux/rockylinux:9.5

# Update all packages in the base image first for security and consistency.
# Then install common dependencies in a single layer to optimize image size.
RUN dnf update -y && \
    dnf install -y epel-release && \
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
    dnf clean all && \
    rm -rf /var/cache/dnf

# Set common environment variables
ENV JAVA_HOME=/usr/lib/jvm/java-11
ENV TZ=Asia/Tokyo