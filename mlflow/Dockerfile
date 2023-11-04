# Use the Python 3.10-slim base image
FROM python:3.10-slim

ARG USER_ID
ARG USER_NAME

ENV HOME="/home/${USER_NAME}"

# Install MLflow and any other Python packages you need
RUN pip install mlflow psycopg2-binary

RUN addgroup --system --gid ${USER_ID} ${USER_NAME} \
    && useradd --system -m --no-log-init --home-dir ${HOME} --uid ${USER_ID} --gid ${USER_NAME} --groups ${USER_NAME} ${USER_NAME}

RUN chown -R ${USER_NAME}:${USER_NAME} ${HOME}
RUN mkdir -p /app && chown -R ${USER_NAME}:${USER_NAME} /app /tmp

USER ${USER_NAME}
# Set the entry point to start the MLflow server
CMD ["mlflow", "server", "--backend-store-uri", "postgresql://mlflow:mlflow@postgres/mlflow", "--default-artifact-root", "/mlflow/artifacts"]
