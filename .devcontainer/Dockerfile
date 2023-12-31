ARG IMAGE_FROM
FROM ${IMAGE_FROM} AS base

################################################################################
#                          NON-ROOT USER CREATION                              #
################################################################################
ENV USER_NAME flutter-dev
ENV USER_HOME /home/${USER_NAME}
RUN adduser --disabled-password --gecos '' ${USER_NAME}

################################################################################
#                          FLUTTER DEPENDENCIES                                #
################################################################################
ARG JAVA_VERSION

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
    rm -rf /var/lib/apt/lists/* && \
    # java installation (for android sdk)
    mkdir -p /tmp/java/ && \
    curl -o /tmp/java/${JAVA_VERSION} https://download.oracle.com/java/21/latest/${JAVA_VERSION} && \
    apt install -y /tmp/java/${JAVA_VERSION} && \
    rm -r /tmp/java/

################################################################################
#                        FLUTTER INSTALLATION SDK                              #
################################################################################
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

################################################################################
#                       CHROME - DEVELOP FOR THE WEB                           #
################################################################################
RUN apt-get update && \
    apt-get install -y \
    # dependencies
    libxcb1 \
    libxcomposite1 \
    libxdamage1 \
    libxext6 \
    libxfixes3 \
    libxkbcommon0 \
    libxrandr2 \
    wget \
    xdg-utils \
    libcairo2 \
    libcups2 \
    libgtk-4-1 \
    libnss3 \
    libpango-1.0-0 \
    libu2f-udev \
    libvulkan1 \
    fonts-liberation \
    libasound2 \
    libatk-bridge2.0-0 \
    libatk1.0-0 \
    libatspi2.0-0 && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* && \
    # installation (develop for web)
    curl -sSL https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb -o chrome.deb && \
    apt-get install -y ./chrome.deb && \
    rm chrome.deb

################################################################################
#                LINUX TOOLCHAIN - DEVELOP FOR LINUX DESKTOP                   #
################################################################################
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

################################################################################
#              ANDROID TOOLCHAIN - DEVELOP FOR ANDROID DEVICES                 #
################################################################################
ARG ANDROID_COMMANNDLINE
ARG ANDROID_PLATFORM_VERSION
ARG ANDROID_BUILD_TOOLS_VERSION

ENV ANDROID_HOME ${USER_HOME}/android-sdk-linux
ENV PATH ${PATH}:${ANDROID_HOME}/cmdline-tools/cmdline-tools/bin:${ANDROID_HOME}/platform-tools:${ANDROID_HOME}/emulator

USER ${USER_NAME}
WORKDIR ${USER_HOME}

RUN curl -o android-sdk-tools.zip https://dl.google.com/android/repository/${ANDROID_COMMANNDLINE} && \
    mkdir -p ${ANDROID_HOME}/ && \
	unzip -q android-sdk-tools.zip -d ${ANDROID_HOME}/cmdline-tools/ && \
	rm android-sdk-tools.zip && \
    # accept all licences
	yes | sdkmanager --licenses && \
	touch $HOME/.android/repositories.cfg && \
    # update to lastest tools version
    sdkmanager --verbose --update && \
    # install NDK because gradle will never install by itself for project
    sdkmanager --verbose ndk-bundle && \
    # install platform and build tools
	sdkmanager --install "cmdline-tools;latest" && \
    sdkmanager "platform-tools" "emulator" "platforms;android-${ANDROID_PLATFORM_VERSION}" "build-tools;${ANDROID_BUILD_TOOLS_VERSION}"

WORKDIR /
################################################################################
#                          FLUTTER CONFIGURATION                               #
################################################################################
# USER root
# Change owner of Flutter SDK to all users
RUN chmod -R a+rx /usr/local/flutter

RUN flutter config --no-analytics && \
    dart --disable-analytics && \
    flutter config --android-sdk "${ANDROID_HOME}" && \
    yes | flutter doctor --android-licenses && \
    flutter doctor -v

# USER ${USER_NAME}