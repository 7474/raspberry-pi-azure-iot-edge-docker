FROM arm32v7/debian:buster-slim
# Install .NET Core 2.1 For Azure IoT Edge
# https://github.com/dotnet/dotnet-docker/blob/master/src/runtime-deps/2.1/stretch-slim/arm32v7/Dockerfile
# Fix version stretch to buster
# - https://packages.debian.org/ja/source/buster/openssl
#    - 1.0 to 1.1
# - https://packages.debian.org/buster/libicu-dev
#    - libicu57 to libicu63
# supported-os
# https://github.com/dotnet/core/blob/master/release-notes/2.1/2.1-supported-os.md
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        ca-certificates \
        \
        # .NET Core dependencies
        libc6 \
        libgcc1 \
        libgssapi-krb5-2 \
        libicu63 \
        liblttng-ust0 \
        libssl1.1 \
        libstdc++6 \
        zlib1g \
    && rm -rf /var/lib/apt/lists/*

ENV \
    # Configure web servers to bind to port 80 when present
    ASPNETCORE_URLS=http://+:80 \
    # Enable detection of running in a container
    DOTNET_RUNNING_IN_CONTAINER=true

# https://github.com/dotnet/dotnet-docker/blob/master/src/runtime/2.1/stretch-slim/arm32v7/Dockerfile
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        curl \
    && rm -rf /var/lib/apt/lists/*

# Install .NET Core
ENV DOTNET_VERSION 2.1.22

# https://dotnet.microsoft.com/download/dotnet-core/2.1
# XXX -k curl failed to verify the legitimacy of the server and therefore could not
RUN curl -SL -k --output dotnet.tar.gz https://dotnetcli.azureedge.net/dotnet/Runtime/$DOTNET_VERSION/dotnet-runtime-$DOTNET_VERSION-linux-arm.tar.gz \
    && dotnet_sha512='6a270a7f12087e430c9adce6b93c6e88d19372cc1964ed7c5e07dc3e60906713c5a2179939b27f1468c6dd53c4c46da55385b9f47883111fc16edc9108c3f890' \
    && echo "$dotnet_sha512 dotnet.tar.gz" | sha512sum -c - \
    && mkdir -p /usr/share/dotnet \
    && tar -zxf dotnet.tar.gz -C /usr/share/dotnet \
    && rm dotnet.tar.gz \
    && ln -s /usr/share/dotnet/dotnet /usr/bin/dotnet

# Install OpenCV v4
# package is v3
# https://packages.debian.org/source/stable/opencv
# opencv (3.2.0+dfsg-6)
# Ref. https://qiita.com/wk_/items/8db529a6b24a955888db
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        build-essential \
        cmake \
        libeigen3-dev \
        libgtk-3-dev \
        qt5-default \
        libvtk7-qt-dev \
        freeglut3-dev \
        libtbb-dev \
        libjpeg-dev \
        libopenjp2-7-dev \
        libpng++-dev \
        libtiff-dev \
        libopenexr-dev \
        libwebp-dev \
        libavresample-dev \
        libhdf5-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

ENV OPENCV_VERSION 4.4.0
RUN cd && mkdir opencv4 && cd opencv4 \
    && git clone https://github.com/opencv/opencv.git \
    && git clone https://github.com/opencv/opencv_contrib.git \
    && cd opencv_contrib \
    && git fetch --all --tags --prune && git checkout ${OPENCV_VERSION} \
    && cd ../opencv \
    && git fetch --all --tags --prune && git checkout ${OPENCV_VERSION} \
    && mkdir build \
    && cd build \
    && cmake \
    -D CMAKE_BUILD_TYPE=Release \
    -D BUILD_DOCS=OFF \
    -D BUILD_EXAMPLES=OFF \
    -D BUILD_TESTS=OFF \
    -D BUILD_PERF_TESTS=OFF \
    -D BUILD_opencv_highgui=OFF \
    -D WITH_CUDA=OFF \
    -D CMAKE_INSTALL_PREFIX=/usr/local \
    -D OPENCV_EXTRA_MODULES_PATH=../../opencv_contrib/modules \
    -S ../ \
    && make -j \
    && make install \
    && cd && -rf ./opencv4

# Build OpenCvSharpExtern
ENV OPENCVSHARP_VERSION 4.4.0.20200916
RUN cd && git clone https://github.com/shimat/opencvsharp.git \
    && cd opencvsharp \
    && git fetch --all --tags --prune && git checkout ${OPENCVSHARP_VERSION} \
    && cd src \
    && mkdir build && cd build \
    && cmake -D CMAKE_INSTALL_PREFIX=/usr/local/include/ .. \
    && make -j \
    && make install \
    && cp OpenCvSharpExtern/libOpenCvSharpExtern.so /usr/local/lib
# cd && rm -rf opencvsharp
# apt remove git && apt remove cmake