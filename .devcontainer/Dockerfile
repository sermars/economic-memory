ARG IMAGE_FROM
FROM ${IMAGE_FROM} AS base

# -> Flutter dependencies
RUN apt-get update && \
    apt-get install -y \
    bash \
    curl \
    file \
    git \
    unzip \
    zip \
    which \
    xz-utils && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# -> Install Flutter SDK
ARG FLUTTER_VERSION
ENV FLUTTER_PATH /usr/local/flutter
RUN curl https://storage.googleapis.com/flutter_infra_release/releases/stable/linux/${FLUTTER_VERSION} -o flutter.tar.xz && \
    tar xf flutter.tar.xz && \
    rm flutter.tar.xz && \
    mv flutter ${FLUTTER_PATH} && \
    # add exception for safe directory
    git config --global --add safe.directory ${FLUTTER_PATH} && \
    # update dart path to flutter SDK (https://docs.flutter.dev/get-started/install/linux#update-your-path)
    old_dart=$(which dart) && \
    if [ -e $(which dart) ]; then rm $(which dart); fi && \
    ln -s ${FLUTTER_PATH}/bin/dart $(old_dart)

# add flutter to PATH
ENV PATH $PATH:${FLUTTER_PATH}/bin

# -> Flutter configuration
RUN flutter config --no-analytics && \
    dart --disable-analytics && \
    flutter doctor -v


FROM base AS linux

# -> Linux desktop dependencies (https://docs.flutter.dev/get-started/install/linux#linux-prerequisites)
RUN apt-get update && \
    apt-get install -y \
    clang \
    cmake \
    ninja-build \
    pkg-config \
    libgtk-3-dev \
    liblzma-dev \
    libstdc++-12-dev && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# -> Flutter configuration
RUN flutter config --no-analytics && \
    dart --disable-analytics && \
    flutter doctor -v

# # -> Android SDK installation
# RUN apt-get update && \
#     apt-get install -y \
#     android-sdk && \
#     apt-get clean && \
#     rm -rf /var/lib/apt/lists/*
# https://dl.google.com/android/repository/commandlinetools-linux-10406996_latest.zip?hl=es-419

# RUN dpkg -L android-sdk
# RUN ls -la /usr/lib
# ENV PATH $PATH:/usr/lib/android-sdk/cmdline-tools/latest/bin
# RUN sdkmanager --version
# RUN sdkmanager --install "cmdline-tools;latest"