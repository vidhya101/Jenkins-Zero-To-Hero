FROM python:3.10-slim

# Install system dependencies and distutils
RUN apt-get update && apt-get install -y \
    python3-distutils \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

# Set the working directory in the container
WORKDIR /app

# Copy only requirements first for caching
COPY requirements.txt /app/

# Install Python dependencies
RUN pip install --no-cache-dir -r requirements.txt

# Copy the application files
COPY . .

# Run database migrations
RUN python manage.py migrate

# Expose the default Django port
EXPOSE 8000

# Command to start the server
CMD ["python", "manage.py", "runserver", "0.0.0.0:8000"]
