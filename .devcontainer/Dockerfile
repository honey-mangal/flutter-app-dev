FROM debian:trixie

# Install dependencies (including native build tools, Chromium for Flutter web, and Docker)
RUN apt-get update && apt-get install -y \
    curl \
    unzip \
    gnupg2 \
    wget \
    clang \
    cmake \
    make \
    openjdk-21-jdk \
    git \
    tar \
    xz-utils \
    pkg-config \
    libgtk-3-dev \
    liblzma-dev \
    ninja-build \
    libstdc++6 \
    ca-certificates \
    lsb-release \
    && rm -rf /var/lib/apt/lists/*

# Install Android SDK command line tools (latest version 13114758)
ENV ANDROID_SDK_ROOT=/usr/local/android-sdk
RUN mkdir -p $ANDROID_SDK_ROOT/cmdline-tools \
    && curl -fo commandlinetools.zip https://dl.google.com/android/repository/commandlinetools-linux-13114758_latest.zip \
    && unzip commandlinetools.zip -d $ANDROID_SDK_ROOT/cmdline-tools \
    && mv $ANDROID_SDK_ROOT/cmdline-tools/cmdline-tools $ANDROID_SDK_ROOT/cmdline-tools/latest \
    && rm commandlinetools.zip

# Install Flutter
RUN mkdir -p /usr/local/development \
    && curl -fo flutter.tar.xz  https://storage.googleapis.com/flutter_infra_release/releases/stable/linux/flutter_linux_3.35.1-stable.tar.xz \
    && tar -xf flutter.tar.xz -C /usr/local/development \
    && rm flutter.tar.xz \

ENV PATH="/usr/local/development/flutter/bin:/usr/local/android-sdk/cmdline-tools/latest/bin:/usr/local/android-sdk/platform-tools:${PATH}"

# Accept Android licenses and install common packages (no emulator/system-images)
RUN yes | $ANDROID_SDK_ROOT/cmdline-tools/latest/bin/sdkmanager --licenses
RUN $ANDROID_SDK_ROOT/cmdline-tools/latest/bin/sdkmanager \
    "platform-tools" \
    "platforms;android-34" \
    "build-tools;34.0.0"

# --- Tailscale install ---
RUN curl -fsSL https://pkgs.tailscale.com/stable/debian/bookworm.noarmor.gpg | tee /usr/share/keyrings/tailscale-archive-keyring.gpg >/dev/null \
    && curl -fsSL https://pkgs.tailscale.com/stable/debian/bookworm.tailscale-keyring.list | tee /etc/apt/sources.list.d/tailscale.list \
    && apt-get update \
    && apt-get install -y tailscale \
    && rm -rf /var/lib/apt/lists/*