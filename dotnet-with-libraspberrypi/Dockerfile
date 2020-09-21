FROM microsoft/dotnet:2.1-runtime-stretch-slim-arm32v7
RUN echo "deb http://mirrordirector.raspbian.org/raspbian/ stretch main contrib non-free rpi firmware" > /etc/apt/sources.list
RUN apt-get update \
    && apt-get install --allow-unauthenticated --no-install-recommends -y \
        libraspberrypi-bin \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*
