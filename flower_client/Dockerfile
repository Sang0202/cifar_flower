# Use an official Python runtime as a parent image
FROM python:3.9-slim

# Set the working directory in the container
WORKDIR /app

# Copy the current directory contents into the container at /app
COPY . /app

# Install Flower and TensorFlow (or any other ML library you need)
RUN pip install flwr torch torchvision 

# Copy the client script into the container
COPY client.py /app/client.py

# Run the Flower client
CMD ["python", "client.py"]
