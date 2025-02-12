# Dockerized-Web-Application-with-CI-CD-Pipeline-GitLab-CI
# Use an official Python runtime as a parent image
FROM python:3.8-slim

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
COPY . /app

# Install any needed packages
RUN pip install --no-cache-dir -r requirements.txt

# Expose port 80
EXPOSE 80

# Run the application when the container launches
CMD ["python", "app/index.py"]
stages:
  - build
  - test
  - deploy

build:
  stage: build
  script:
    - docker build -t myapp .
    - docker tag myapp registry.gitlab.com/username/myapp:latest
    - docker push registry.gitlab.com/username/myapp:latest

test:
  stage: test
  script:
    - pytest tests/

deploy:
  stage: deploy
  script:
    - kubectl apply -f k8s/deployment.yaml
    apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: web-app
  template:
    metadata:
      labels:
        app: web-app
    spec:
      containers:
      - name: web-app
        image: registry.gitlab.com/username/myapp:latest
        ports:
        - containerPort: 80
        provider "aws" {
  region = "us-west-2"
}

resource "aws_instance" "my_ec2_instance" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"
  tags = {
    Name = "MyInstance"
  }
}

resource "aws_s3_bucket" "my_bucket" {
  bucket = "my-terraform-bucket"
  acl    = "private"
}

resource "aws_rds_instance" "my_rds" {
  identifier = "mydb"
  instance_class = "db.t2.micro"
  allocated_storage = 20
  engine = "mysql"
  username = "admin"
  password = "password"
  db_name = "mydb"
}
stages:
  - init
  - plan
  - apply

init:
  stage: init
  script:
    - terraform init

plan:
  stage: plan
  script:
    - terraform plan

apply:
  stage: apply
  script:
    - terraform apply -auto-approve
    # Use an official Python runtime as a parent image
FROM python:3.8-slim

# Set the working directory to /service1
WORKDIR /service1

# Copy the current directory contents into the container at /service1
COPY . /service1

# Install any needed packages
RUN pip install --no-cache-dir -r requirements.txt

# Expose port 80
EXPOSE 80

# Run the application when the container launches
CMD ["python", "app.py"]
# Use an official Python runtime as a parent image
FROM python:3.8-slim

# Set the working directory to /service2
WORKDIR /service2

# Copy the current directory contents into the container at /service2
COPY . /service2

# Install any needed packages
RUN pip install --no-cache-dir -r requirements.txt

# Expose port 80
EXPOSE 80

# Run the application when the container launches
CMD ["python", "app.py"]
stages:
  - build
  - deploy

build:
  stage: build
  script:
    - docker build -t service1 ./service1
    - docker build -t service2 ./service2
    - docker push service1
    - docker push service2

deploy:
  stage: deploy
  script:
    - kubectl apply -f k8s/deployment.yaml
k8s/deployment.yaml
yaml
Copy
Edit
apiVersion: apps/v1
kind: Deployment
metadata:
  name: service1
spec:
  replicas: 2
  selector:
    matchLabels:
      app: service1
  template:
    metadata:
      labels:
        app: service1
    spec:
      containers:
      - name: service1
        image: service1:latest
        ports:
        - containerPort: 80

---

apiVersion: apps/v1
kind: Deployment
metadata:
  name: service2
spec:
  replicas: 2
  selector:
    matchLabels:
      app: service2
  template:
    metadata:
      labels:
        app: service2
    spec:
      containers:
      - name: service2
        image: service2:latest
        ports:
        - containerPort: 80
