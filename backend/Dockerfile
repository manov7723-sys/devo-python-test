# syntax=docker/dockerfile:1
FROM python:3.12-slim
ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1
WORKDIR /app
COPY requirements.txt ./
RUN pip install --no-cache-dir -r requirements.txt
# Ensure the production server ("uvicorn") is present even if it's
# missing from requirements.txt — otherwise the CMD fails with
# "uvicorn: not found" at container start.
RUN pip install --no-cache-dir "uvicorn[standard]"
COPY . .
# Non-root runtime user. USER must be a NUMERIC uid — Kubernetes'
# runAsNonRoot check refuses to start a container whose USER is a name,
# because it cannot verify from outside the image that the name is non-root
# (2026-09, java-app-backend hit CreateContainerConfigError under the
# restricted profile because USER was "app" instead of a number).
RUN useradd --create-home --uid 10001 appuser && chown -R appuser /app
USER 10001
EXPOSE 8000
CMD ["uvicorn","app.main:app","--host","0.0.0.0","--port","8000"]
