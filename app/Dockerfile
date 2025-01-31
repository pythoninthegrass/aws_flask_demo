# syntax=docker/dockerfile:1.7.0

ARG PYTHON_VERSION=3.11.11

FROM python:${PYTHON_VERSION}-slim as builder

COPY . /src

RUN python -m pip install --user fastapi uvicorn boto3 flask

FROM python:${PYTHON_VERSION}-slim as app

COPY --from=builder /root/.local /root/.local

COPY --from=builder /src .

ENV PATH=/root/.local:$PATH

EXPOSE 5000

CMD ["python", "app.py"]
