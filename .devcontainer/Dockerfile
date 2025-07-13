FROM ubuntu:22.04
ENV DEBIAN_FRONTEND=noninteractive
RUN apt update && apt install -y --no-install-recommends \
    build-essential \
    clang \
    cmake \
    ninja-build \
    gdb \
    lldb \
    valgrind \
    git \
    clangd \
    && rm -rf /var/lib/apt/lists/*
WORKDIR /workspace