FROM python:3

# Install distutils package
RUN apt-get update && apt-get install -y python3-distutils

# Install Django 3.2
RUN pip install django==3.2

# Copy the project files
COPY . .

# Run database migrations
RUN python manage.py migrate
