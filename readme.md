# Flask Application Deployment with Ansible and Docker

## Overview

This project demonstrates the deployment of a Flask web application using Ansible for automation and Docker for containerization. The goal is to streamline the deployment process and manage the application efficiently.

## Prerequisites

- Ansible installed on your master machine (macOS).
- Docker installed on the target Ubuntu server.
- SSH access to the Ubuntu server.

## Project Structure

 ├── app.py ├── Dockerfile ├── inventory.ini ├── deploy.yml ├── requirements.txt └── README.md


- **`app.py`**: The Flask application code.
- **`Dockerfile`**: The Dockerfile to build the Flask app image.
- **`requirements.txt`**: The Python dependencies for the Flask app.
- **`inventory.ini`**: Ansible inventory file specifying the target hosts.
- **`deploy.yml`**: Ansible playbook that installs Docker, pulls the Flask app image, and runs the container.

## Getting Started

### 1. Create Flask Application

Create the `app.py` file with the following content:

```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello():
    return "Hello, World! This is my Flask app running in a Docker container."

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=80)
```

### 2. Create Dockerfile

Create a `Dockerfile` with the following content:

```dockerfile
# Use the official Python image from the Docker Hub
FROM python:3.9-slim

# Set the working directory
WORKDIR /app

# Copy the requirements file and install dependencies
COPY requirements.txt ./
RUN pip install --no-cache-dir -r requirements.txt

# Copy the application code
COPY app.py ./

# Expose port 80
EXPOSE 80

# Command to run the application
CMD ["python", "app.py"]
```

### 3. Create Requirements File
Create a 'requirements.txt' file to specify the Flask dependency:

```python
Flask==2.0.3
Werkzeug==2.0.3
```

### 4. Update Inventory
Edit the `inventory.ini` file to include your target server's IP address and SSH user:

```python
[app_servers]
ubuntu ansible_host=192.168.64.5 ansible_user=ubuntu
```

### 5. Write Your Playbook

Ensure that your `deploy.yml` playbook is correctly configured to install Docker, build the Flask app image, and start the container. Here’s an example:

```yml
---
- hosts: app_servers
  become: true
  tasks:
    - name: Install Docker
      apt:
        name: docker.io
        state: present
        update_cache: true

    - name: Build Flask app Docker image
      docker_image:
        path: .
        name: your-username/flask-app
        state: present

    - name: Stop and remove any existing container
      docker_container:
        name: flask_app
        state: absent

    - name: Run the Flask app container
      docker_container:
        name: flask_app
        image: your-username/flask-app
        state: started
        ports:
          - "80:80"
```

### 6.  Run the Playbook

Execute the Ansible playbook with the following command:

```python
ansible-playbook -i inventory.ini deploy.yml
```

### 7. Access Your Application

Once the playbook runs successfully, you can access your Flask app in your web browser at:

```python
http://<your-ubuntu-ip>:80
```

### 8. Verify Your Deployment

You can verify that your Flask app is running correctly by checking the logs of the Docker container:

```python
docker logs flask_app
```

