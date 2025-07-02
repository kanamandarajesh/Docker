```
# Use the official Python image from Docker Hub
FROM python:3.11-slim

# Set the working directory in the container
WORKDIR /app

# Copy the current directory contents into the container at /app
COPY . /app

# Install any necessary dependencies
RUN pip install --no-cache-dir -r requirements.txt

# Expose port 5000 (or any other port your app uses)
EXPOSE 5000

# Set the environment variable for Python
ENV PYTHONUNBUFFERED 1

# Command to run your Python application
CMD ["python", "app.py"]
```

Below is for python multistage build dockerfile

```
# ---------- Stage 1: Build stage ----------
FROM python:3.11-slim AS build

# Set the working directory
WORKDIR /app

# Install build dependencies (for packages like numpy, pandas, etc. if needed)
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    gcc \
    && rm -rf /var/lib/apt/lists/*

# Copy the application code
COPY . /app

# Install Python dependencies into a virtual environment
RUN python -m venv /venv && \
    /venv/bin/pip install --upgrade pip && \
    /venv/bin/pip install --no-cache-dir -r requirements.txt

# ---------- Stage 2: Runtime stage ----------
FROM python:3.11-slim

# Set environment variables
ENV PYTHONUNBUFFERED=1 \
    PATH="/venv/bin:$PATH"

# Copy virtual environment and app from build stage
COPY --from=build /venv /venv
COPY --from=build /app /app

# Set working directory
WORKDIR /app

# Expose port (adjust if needed)
EXPOSE 5000

# Run the application
CMD ["python", "app.py"]
```
