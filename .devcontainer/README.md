docker build \
    --progress=plain \
    --target base \
    --build-arg IMAGE_FROM="dart:3.2.4-sdk" \
    --build-arg FLUTTER_VERSION="flutter_linux_3.16.5-stable.tar.xz" \
    --build-arg ANDROID_COMMANNDLINE="commandlinetools-linux-10406996_latest.zip" \
    --build-arg ANDROID_PLATFORM_VERSION="33" \
    --build-arg ANDROID_BUILD_TOOLS_VERSION="33.0.2" \
    --build-arg JAVA_VERSION="jdk-21_linux-x64_bin.deb" \
    -t prueba_flutter .