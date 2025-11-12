FROM python:3.11-slim

# Avoid interactive prompts during apt installs
ENV DEBIAN_FRONTEND=noninteractive
ENV PYTHONUNBUFFERED=1

# Install common build and runtime deps useful for this repo
# - build-essential/gcc: compile native extensions
# - libpq-dev / postgresql-client: for building/connecting to Postgres-backed code
# - python3-venv: allow creating venvs inside the container if desired
RUN apt-get update \
  && apt-get install -y --no-install-recommends \
    build-essential \
    gcc \
    python3-dev \
    libssl-dev \
    libffi-dev \
    pkg-config \
    libpq-dev \
    postgresql-client \
    libldap2-dev \
    libsasl2-dev \
    python3-venv \
    git \
    curl \
    unzip \
    procps \
    libxext6 \
    libxrender1 \
    libxtst6 \
    libxi6 \
    libfreetype6 \
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/*

# Set working directory for the project
WORKDIR /workspace

# Copy repository into the image so pip can install local editable packages.
# This keeps build behavior explicit and ensures the ./nipap* paths exist.
COPY . /workspace

ENV PYTHONPATH="/workspace/nipap/"

# Ensure default config exists in /etc/nipap so nipapd can start in the devcontainer.
# Copying the provided distro config into /etc/nipap keeps behavior consistent with
# system packages and CI scripts that expect /etc/nipap/nipap.conf to exist.

RUN mkdir -p /etc/nipap && \
      cp /workspace/nipap/nipap.conf.dist /etc/nipap/nipap.conf;

# Install project packages and dependencies so the image has Tornado and other
# runtime requirements available when running via docker-compose.
RUN python -m pip install --upgrade pip setuptools wheel \
  && pip install --no-cache-dir -e ./nipap ./nipap-cli ./nipap-www ./pynipap

RUN useradd -m nipap -u 10001 --user-group
RUN chown -R nipap:nipap /etc/nipap
USER nipap

RUN python3 nipap/nipap/nipap_passwd.py create-database
RUN python3 nipap/nipap/nipap_passwd.py add --username nipap --name "NIPAP user" --password papin

EXPOSE 1337
EXPOSE 5000

# Default command leaves the container running so IDEs can attach
CMD ["sleep", "infinity"]
