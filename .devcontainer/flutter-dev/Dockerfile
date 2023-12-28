ARG IMAGE_FROM
FROM ${IMAGE_FROM}

RUN apt-get update && \
    apt-get install -y \
    bash \
    curl \
    file \
    git \
    mkdir \
    rm \
    unzip \
    zip \
    which \
    xz-utils && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Install Flutter SDK
ARG FLUTTER_VERSION
RUN curl https://storage.googleapis.com/flutter_infra_release/releases/stable/linux/${FLUTTER_VERSION} -o flutter.tar.xz && \
    tar xf flutter.tar.xz && \
    rm flutter.tar.xz && \
    mv flutter /usr/local/flutter

# Add Flutter to PATH
ENV PATH $PATH:/usr/local/flutter/bin

# Run Flutter doctor
RUN flutter doctor -v