Getting started with NIPAP using Docker Compose
==================================================

This guide will help you get NIPAP up and running quickly using Docker Compose,
which provides a containerized development and demonstration environment with
all necessary services pre-configured.

Prerequisites
-------------

Before you begin, ensure you have the following installed on your system:

* Docker (version 20.10 or later)
* Docker Compose (version 1.29 or later)

You can verify your installation by running::

    docker --version
    docker-compose --version

For installation instructions, visit `Docker's official documentation
<https://docs.docker.com/get-docker/>`_.

Quick Start
-----------

1. Clone the NIPAP repository::

    git clone https://github.com/SpriteLink/NIPAP.git
    cd NIPAP

2. Start all services using Docker Compose::

    docker-compose -f .devcontainer/docker-compose.yml up -d

   This command will:

   * Build the NIPAP development image
   * Start the nipapd backend service (port 1337)
   * Start the nipap-www web interface (port 5000)
   * Start a PostgreSQL database (port 5432)
   * Start Kafka and Kafdrop for message handling (ports 9092, 9000)

3. Wait for all services to be healthy. You can check the status with::

    docker-compose -f .devcontainer/docker-compose.yml ps

   The database service should show a healthy status after a few seconds.

4. Access NIPAP:

   * **Web Interface**: http://localhost:5000
   * **nipapd Backend**: localhost:1337
   * **Kafka UI (Kafdrop)**: http://localhost:9000
   * **PostgreSQL**: localhost:5432

Services Overview
------------------

The Docker Compose setup includes the following services:

**nipapd**
    The NIPAP backend daemon providing the XML-RPC API and core functionality.
    Port: 1337

**nipap-www**
    The NIPAP web user interface built with Flask.
    Port: 5000

**db (PostgreSQL)**
    The PostgreSQL database backend for storing IP address data.
    Port: 5432
    Username: nipap
    Password: papin
    Database: nipap

**kafka**
    Apache Kafka message broker for event streaming.
    Port: 9092

**kafdrop**
    Web UI for monitoring Kafka topics and messages.
    Port: 9000

Stopping the Services
---------------------

To stop all running services::

    docker-compose -f .devcontainer/docker-compose.yml stop

To stop and remove all containers (preserving the database volume)::

    docker-compose -f .devcontainer/docker-compose.yml down

To stop and remove everything including the database volume::

    docker-compose -f .devcontainer/docker-compose.yml down -v

Viewing Logs
------------

To view logs from all services::

    docker-compose -f .devcontainer/docker-compose.yml logs -f

To view logs from a specific service (e.g., nipapd)::

    docker-compose -f .devcontainer/docker-compose.yml logs -f nipapd

Accessing the Containers
------------------------

To open a shell in a running container::

    docker-compose -f .devcontainer/docker-compose.yml exec nipapd /bin/bash

Or for other services::

    docker-compose -f .devcontainer/docker-compose.yml exec db psql -U nipap -d nipap

Development
-----------

The development environment is set up to allow live code changes:

* Source files from your local machine are mounted into the containers
* Changes to Python files are immediately reflected (some services may need
  restart for changes to take effect)
* The database is persisted in the ``db-data`` volume between restarts

To rebuild the development image after making changes::

    docker-compose -f .devcontainer/docker-compose.yml build

Troubleshooting
---------------

**Services fail to start**
    Check the logs to see what went wrong::

        docker-compose -f .devcontainer/docker-compose.yml logs

**Can't connect to NIPAP web interface**
    Ensure port 5000 is not in use by another service. You can either:

    * Stop the conflicting service
    * Modify the port mapping in ``docker-compose.yml`` (change ``5000:5000``
      to ``5001:5000`` for example)

**Database connection errors**
    Wait a few seconds for the PostgreSQL service to fully initialize. Check
    the database health with::

        docker-compose -f .devcontainer/docker-compose.yml ps

    The database should show a ``healthy`` status.

**Out of disk space**
    The Docker images and volumes can consume significant space. To clean up
    unused Docker resources::

        docker system prune -a --volumes

    Use this command carefully as it will remove all unused images, containers,
    and volumes.

Next Steps
----------

Now that you have NIPAP running, check out:

* `NIPAP configuration <config-nipapd.rst>`_ to customize your setup
* `CLI configuration <config-cli.rst>`_ to configure the command-line interface
* `Web UI configuration <config-www.rst>`_ to configure the web interface
* `Development guide <getting-started-development.rst>`_ for detailed
  development information

For more information about NIPAP features and usage, visit
http://SpriteLink.github.io/NIPAP
