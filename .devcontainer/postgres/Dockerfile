FROM postgres:14-bullseye

ENV DEBIAN_FRONTEND=noninteractive

# Install build dependencies needed to compile ip4r
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
       build-essential \
       ca-certificates \
       git \
       wget \
       curl \
       gnupg \
       lsb-release \
       ca-certificates \
       postgresql-server-dev-14 \
       pkg-config \
       libssl-dev \
    && rm -rf /var/lib/apt/lists/*

# Add ip4r build/install steps
RUN git clone https://github.com/RhodiumToad/ip4r.git \
    && cd ip4r \
    && make \
    && make install \
    && cd .. \
    && rm -rf ip4r
