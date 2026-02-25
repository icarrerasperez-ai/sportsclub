# docker/app/Dockerfile
FROM python:3.13-slim-trixie
EXPOSE 8080

# Set environment variables
ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1

# Crear el usuario Nuevo
RUN useradd -ms /bin/bash appuser

# Set work directory
WORKDIR /app

# Cambiando el usuario propietario de la carpeta
RUN chown appuser:appuser /app

# Install system dependencies
RUN apt-get update && apt-get install -y \
    curl \
    gcc \
    python3-dev \
    libpq-dev \
    && rm -rf /var/lib/apt/lists/*

# Install Python dependencies
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
RUN pip install --no-cache-dir uvicorn

# Copy project
COPY --chown=appuser:appuser sportsclub/ .

# Cambiar de usuario
USER appuser 

# Collect static files (needed when serving through a reverse proxy)
RUN python manage.py collectstatic --noinput

# Run uvicorn
CMD ["uvicorn", "sportsclub.asgi:application", "--host", "0.0.0.0", "--port", "8080"]