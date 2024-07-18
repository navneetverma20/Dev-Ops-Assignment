# Dev-Ops-Assignment

To achieve the goal of automating the testing process of a Python Django-based web application within a Docker container using Jenkins, follow the steps outlined below. This guide will cover the development of the web application, the creation of the Dockerfile, the setup of the Jenkins pipeline, and the documentation of the process in a GitHub repository.

### Step 1: Develop a Django Web Application with Test Cases

1. **Set Up Django Project**

   ```bash
   django-admin startproject my_django_app
   cd my_django_app
   python manage.py startapp my_app
   ```

2. **Create Test Cases**

   Add the following to `my_app/tests.py`:

   ```python
   from django.test import TestCase
   from django.urls import reverse

   class SimpleTestCase(TestCase):
       def test_homepage(self):
           response = self.client.get(reverse('home'))
           self.assertEqual(response.status_code, 200)
   ```

3. **Configure URLs and Views**

   - `my_django_app/urls.py`:

     ```python
     from django.contrib import admin
     from django.urls import path, include

     urlpatterns = [
         path('admin/', admin.site.urls),
         path('', include('my_app.urls')),
     ]
     ```

   - `my_app/urls.py`:

     ```python
     from django.urls import path
     from . import views

     urlpatterns = [
         path('', views.home, name='home'),
     ]
     ```

   - `my_app/views.py`:

     ```python
     from django.shortcuts import render

     def home(request):
         return render(request, 'home.html')
     ```

4. **Create Requirements File**

   - `requirements.txt`:

     ```
     Django>=3.0,<4.0
     pytest-django
     ```

### Step 2: Create a Dockerfile

Create a Dockerfile to build the Docker image for your Django application.

- `Dockerfile`:

  ```Dockerfile
  # Use an official Python runtime as a parent image
  FROM python:3.9-slim

  # Set the working directory
  WORKDIR /app

  # Copy the current directory contents into the container at /app
  COPY . /app

  # Install any needed packages specified in requirements.txt
  RUN pip install --no-cache-dir -r requirements.txt

  # Run tests
  CMD ["pytest"]
  ```

### Step 3: Set Up Jenkins Pipeline

Create a `Jenkinsfile` to define the Jenkins pipeline.

- `Jenkinsfile`:

  ```groovy
  pipeline {
      agent any

      stages {
          stage('Checkout') {
              steps {
                  // Pull the source code from the repository
                  git 'https://github.com/your-repository-url.git'
              }
          }

          stage('Build Docker Image') {
              steps {
                  script {
                      dockerImage = docker.build("my_django_app:latest")
                  }
              }
          }

          stage('Run Tests') {
              steps {
                  script {
                      dockerImage.inside {
                          sh 'pytest --junitxml=report.xml'
                      }
                  }
              }
              post {
                  always {
                      junit 'report.xml'
                  }
              }
          }
      }
  }
  ```

### Step 4: Set Up Jenkins

1. **Install Jenkins**: Follow the official Jenkins documentation to install Jenkins on your system.
2. **Install Docker**: Ensure Docker is installed and running on the Jenkins server.
3. **Install Plugins**: Install necessary plugins like Docker, Git, and JUnit in Jenkins.
4. **Create a New Pipeline Job**:
   - Open Jenkins and create a new pipeline job.
   - Set the pipeline script to use the `Jenkinsfile` from your repository.

### Step 5: Create a GitHub Repository and Document the Process

1. **Initialize a GitHub Repository**

   ```bash
   git init
   git remote add origin https://github.com/your-username/your-repo.git
   ```

2. **Push the Project to GitHub**

   ```bash
   git add .
   git commit -m "Initial commit"
   git push -u origin master
   ```

3. **Document the Process in the README File**

   - `README.md`:

     ```markdown
     # Django CI/CD with Jenkins and Docker

     This project demonstrates how to set up Continuous Integration (CI) for a Django application using Jenkins and Docker.

     ## Prerequisites

     - Docker
     - Jenkins
     - GitHub account

     ## Project Structure

     ```
     my_django_app/
     ├── Dockerfile
     ├── Jenkinsfile
     ├── manage.py
     ├── my_app/
     │   ├── __init__.py
     │   ├── models.py
     │   ├── tests.py
     │   ├── views.py
     ├── requirements.txt
     └── my_django_app/
         ├── __init__.py
         ├── settings.py
         ├── urls.py
         ├── wsgi.py
     ```

     ## Steps

     1. **Clone the Repository**
        ```bash
        git clone https://github.com/your-username/your-repo.git
        ```

     2. **Build Docker Image**
        ```bash
        docker build -t my_django_app:latest .
        ```

     3. **Run Tests**
        ```bash
        docker run my_django_app:latest
        ```

     ## Jenkins Pipeline

     The Jenkins pipeline is defined in the `Jenkinsfile`. It performs the following steps:
     1. Pulls the source code from the GitHub repository.
     2. Builds the Docker image.
     3. Runs the tests inside the Docker container.
     4. Reports the test results.

     ## Screenshots

     ![Jenkins Dashboard](images/jenkins_dashboard.png)
     ![Jenkins Pipeline](images/jenkins_pipeline.png)
     ![Test Results](images/test_results.png)
     ```

### Conclusion

By following these steps, you will have successfully automated the testing process of your Django-based web application using Jenkins within a Docker container. The project will be documented in a GitHub repository with a detailed README file, including images and reports for Docker and Jenkins.
