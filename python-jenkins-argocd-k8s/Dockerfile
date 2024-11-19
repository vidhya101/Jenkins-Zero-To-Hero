FROM python:3.9-slim
ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1
WORKDIR /app

# Install system dependencies
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
    gcc \
    python3-dev \
    libpq-dev && \
    rm -rf /var/lib/apt/lists/*

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy all files and immediately list directory contents
COPY . .
RUN ls -la /app

# Continue with your original commands
RUN python manage.py migrate && python manage.py collectstatic --noinput
EXPOSE 8000
CMD ["python", "manage.py", "runserver", "0.0.0.0:8000"]
