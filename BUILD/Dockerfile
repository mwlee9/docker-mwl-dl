FROM python:3.11.5 as compile-image

### Set Pyspark Python Paths so that Driver and Executor are consistent
ENV PYTHONPATH=/usr/bin/python3.11.5
ENV PYSPARK_PYTHON=/usr/bin/python3.11.5
ENV PYSPARK_DRIVER_PYTHON=/usr/bin/python3.11.5

### Setup a virtual env for python isolation and smaller image sizes on multi-stage builds.
### Allows us to easily copy over the files in one bundle from compile container to build.
ENV VIRTUAL_ENV=/opt/venv
RUN python -m venv $VIRTUAL_ENV
ENV PATH="$VIRTUAL_ENV/bin:$PATH"

### Copy requirements file and install packages to the venv (hence no --user flag).
### If VENV is not used, --user flag must be on pip install otherwise git and requirements deps will collide.
### Adjust this file before build to add additional modules, minimal build to save on space
RUN pip install --upgrade pip
COPY requirements.txt ./requirements.txt
RUN pip install --no-cache-dir -r requirements.txt

### Install Jupyter and extensions
# RUN python -m pip install --no-cache-dir jupyterlab jupyter_contrib_nbextensions jupyter_nbextensions_configurator

########################################################################################
### Change to the slim image to save on space - ONLY RECOMMENDED FOR STANDALONE PY APPS, according to Python Docker Repo.
FROM python:3.11.5 AS build-image

### Set the venv to path
ENV PATH="/opt/venv/bin:$PATH"

### Need to make this directory for openjdk to install properly
RUN mkdir -p /usr/share/man/man1


### HW1-4
# ### Alternative linux dependencies for DL
# RUN apt-get update && apt-get install -y --no-install-recommends wget && \
    # rm -rf /var/lib/apt/lists/*

# Fix DL4006
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

USER root

WORKDIR /home/

### Copy the files from the previous stage to save on space and remove secrets.
COPY --from=compile-image /opt/venv /opt/venv