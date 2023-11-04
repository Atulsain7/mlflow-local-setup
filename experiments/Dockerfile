# Use the official Python 3.10 image as the base image
FROM python:3.10-slim

# Declare build-time arguments with default values
ARG USER_ID
ARG USER_NAME
ARG USER_EMAIL

# Set the build-time arguments as environment variables
ENV USER_ID=${USER_ID}
ENV USER_NAME=${USER_NAME}
ENV USER_EMAIL=${USER_EMAIL}
ENV HOME="/home/${USER_NAME}"
ENV VIRTUAL_ENV="/home/${USER_NAME}/venv"

# Set environment variables
ENV PYTHONUNBUFFERED=1 \
    DEBIAN_FRONTEND=noninteractive \
    PATH="usr/local/gcloud/google-cloud-sdk/bin:${HOME}/.local/bin:${VIRTUAL_ENV}/bin:${PATH}" \
    TZ=Asia/Kolkata \
    BUILD_POETRY_LOCK="${HOME}/poetry.lock.build"

# Install required packages and clean up
RUN apt-get update -qq && \
    apt-get install -qq -y vim gcc curl git build-essential libb64-dev software-properties-common && \
    rm -rf /var/lib/apt/lists/* && \
    apt-get clean

# Create a system user and set up their home directory
RUN addgroup --system --gid ${USER_ID} ${USER_NAME} && \
    useradd --system -m --no-log-init --home-dir ${HOME} --uid ${USER_ID} --gid ${USER_NAME} --groups ${USER_NAME} ${USER_NAME} && \
    mkdir -p /project && \
    chown -R ${USER_NAME}:${USER_NAME} ${HOME} /project /tmp

# Install Poetry
RUN curl -sSL https://install.python-poetry.org | python3 -

# Switch to the user
USER ${USER_NAME}

# Copy and set up your Python project files
COPY pyproject.toml *.lock /experiments/
WORKDIR /experiments

# Install project dependencies and clean up
RUN poetry config virtualenvs.create false && \
    poetry config installer.max-workers 10 && \
    python3 -m venv ${VIRTUAL_ENV} && \
    pip install --upgrade pip setuptools && \
    poetry install && \
    rm -rf ${HOME}/.cache/*

# Switch back to the root user to copy scripts
# USER root

# Copy scripts and set permissions
# COPY ./docker/scripts/* /
# RUN chown ${USER_NAME} /*.sh && \
#     chmod +x /*.sh

# Switch back to the user
# USER ${USER_NAME}

# Configure Git for the user
# RUN git config --global user.email "${USER_EMAIL}" && \
#     git config --global user.name "${USER_NAME}"

# Use "bash" as the entrypoint with the correct syntax
CMD ["scripts/startup-script.sh"]
