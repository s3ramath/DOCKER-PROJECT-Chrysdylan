Dockerfile # Use Python 3.9.16 Alpine as the base image
FROM python:3.9.16-alpine

# Set environment variables
ENV PYTHONDONTWRITEBYTECODE 1
ENV PYTHONUNBUFFERED 1

# Install dependencies
RUN apk update && apk add --no-cache \
    gcc \
    musl-dev \
    libffi-dev \
    python3-dev \
    jpeg-dev \
    zlib-dev \
    postgresql-dev \
    build-base \
    bash

# Set working directory
WORKDIR /app

# Copy requirements first to leverage Docker cache
COPY requirements.txt .

# Install Python packages
RUN pip install --upgrade pip && pip install -r requirements.txt

# Copy rest of the project
COPY . .

# Collect static files (optional — useful for production)
RUN python manage.py collectstatic --noinput

# Expose port 8000
EXPOSE 8000

# Default command
CMD ["python", "manage.py", "runserver", "0.0.0.0:8000"]
