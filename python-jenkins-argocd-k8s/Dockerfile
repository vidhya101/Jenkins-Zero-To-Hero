FROM python:3.13-slim

# Install necessary dependencies, including distutils
RUN apt-get update && apt-get install -y python3-distutils python3-apt && \
    apt-get clean && rm -rf /var/lib/apt/lists/*

# Install Django
RUN pip install django==3.2

# Copy application code
COPY . .

# Run database migrations
RUN python manage.py migrate

# Expose the default Django development server port
EXPOSE 8000

# Command to start the application
CMD ["python", "manage.py", "runserver", "0.0.0.0:8000"]
