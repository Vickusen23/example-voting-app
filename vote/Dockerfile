# Use a Python 3.12 slim base image
FROM python:3.12-slim AS base

# Install curl for the container healthcheck
RUN apt-get update && \
    apt-get install -y --no-install-recommends curl && \
    rm -rf /var/lib/apt/lists/*

# Set the application directory
WORKDIR /usr/local/app

# Install Python dependencies
COPY requirements.txt ./requirements.txt
RUN pip install --no-cache-dir -r requirements.txt

# Create a non-root user
RUN useradd --create-home --uid 10001 appuser

# Development stage
FROM base AS dev
RUN pip install --no-cache-dir watchdog
ENV FLASK_ENV=development
USER appuser
CMD ["python", "app.py"]

# Production stage
FROM base AS final
COPY --chown=appuser:appuser . .

# Run as a non-root user
USER appuser

# Application listens on port 8080
EXPOSE 8080

CMD ["gunicorn", "app:app", "-b", "0.0.0.0:8080", "--log-file", "-", "--access-logfile", "-", "--workers", "4", "--keep-alive", "0"]
