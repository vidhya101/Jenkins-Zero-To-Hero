FROM python:3

# Install distutils to resolve the missing module error
RUN apt-get update && apt-get install -y python3-distutils

# Install Django
RUN pip install django==3.2

# Copy application code into the container
COPY . .

# Run database migrations
RUN python manage.py migrate

# Expose port 8000 for the Django development server
EXPOSE 8000

# Default command to run the application
CMD ["python", "manage.py", "runserver", "0.0.0.0:8000"]
