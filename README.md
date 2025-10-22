# docker-networking-and-logging
Docker assignment including a multi-container setup with a custom logging container, Nginx web server, and Fluentd log collector. Demonstrates Docker volumes, networking, and resource limits.
This repository contains a Docker-based project demonstrating key containerization concepts:
A custom logging container using cron to log timestamps every minute
An Nginx container serving static web content on port 8080
A Fluentd container collecting logs from shared volumes and printing to stdout
Docker networking using a custom bridge network for container communication
Persistent data storage using Docker volumes
Resource limits applied to containers for CPU and memory control
Includes a complete setup using Docker Compose
Hi All 
Below is an docker assignment for you
Complete it and let us know 
I have given the steps aswell, go through them and understand each concepts 
‘’’
1. Create a Dockerfile for a Custom Logging Container
• Inside the container:
• Install cron
• Create a cron job that writes the time to a log file every minute
• Mount a volume at /var/logs/ to persist logs
⸻
2. Set Up Nginx
• Mount a local HTML file (index.html) using a Docker volume
• Expose port 8080 and check that Nginx serves your content
⸻
3. Add Fluentd to Collect Logs
• Use fluent/fluentd image
• Configure it to:
• Read logs from the volume shared with your custom Alpine container
• Print those logs to stdout
⸻
4. Docker Networking
• Create a custom bridge network
• Connect all containers to it
• Ensure Nginx and Fluentd can talk to other containers via container names 
⸻
5. Logging Validation
• Check that your cron job logs are:
• Written to volume
• Picked up by Fluentd
• Printed to stdout
⸻
6. Clean Restart Test
• Stop and remove all containers
• Start again
• Ensure:
• Logs are still there (volume works)
• Cron restarts
• Nginx still serves HTML
⸻
7. Limit CPU and memory usage for each container

🐳 Docker Multi-Container Assignment
📘 Overview
This project demonstrates the use of Docker containers to set up:
A Custom Logging Container that logs the current time every minute using cron
An Nginx web server serving static content
A Fluentd container collecting and printing logs from shared volumes
Docker networking for inter-container communication
Volumes for data persistence
Resource limits for CPU and memory management

docker-multi-container-assignment/
│
├─ cron-logger/
│   ├─ Dockerfile
│   ├─ cronjob.sh
│   └─ logs/                # persisted logs volume
│
├─ nginx/
│   ├─ Dockerfile
│   └─ index.html
│
├─ fluentd/
│   ├─ fluent.conf
│
├─ docker-compose.yml
└─ README.md
1. Custom Logging Container

File: cron-logger/Dockerfile
FROM alpine:latest

RUN apk add --no-cache bash curl tzdata busybox-suid \
    && apk add --no-cache openrc cron

RUN mkdir -p /var/logs

COPY cronjob.sh /cronjob.sh
RUN chmod +x /cronjob.sh

RUN echo "* * * * * /cronjob.sh >> /var/logs/cron.log 2>&1" > /etc/crontabs/root

CMD ["crond", "-f", "-d", "8"]

File: cron-logger/cronjob.sh

#!/bin/sh
echo "Current time: $(date)"

🌐 2. Nginx Setup
File: nginx/Dockerfile
FROM nginx:alpine
COPY index.html /usr/share/nginx/html/index.html
EXPOSE 8080

File: nginx/index.html
<!DOCTYPE html>
<html>
<head>
  <title>Docker Nginx Page</title>
</head>
<body>
  <h1>Welcome to the Docker Assignment!</h1>
</body>
</html>
You can access it at http://localhost:8080

📦 3. Fluentd Configuration

File: fluentd/fluent.conf
<source>
  @type tail
  path /var/logs/cron.log
  pos_file /var/log/cron.pos
  tag cron.logs
  format none
</source>

<match **>
  @type stdout
</match>
Fluentd reads the cron logs and prints them to stdout.

🔗 4. Docker Compose Setup
File: docker-compose.yml
version: "3.9"

services:
  cron-logger:
    build: ./cron-logger
    volumes:
      - cron-logs:/var/logs
    deploy:
      resources:
        limits:
          cpus: '0.5'
          memory: 256M
    networks:
      - mynet

  nginx:
    build: ./nginx
    ports:
      - "8080:80"
    networks:
      - mynet

  fluentd:
    image: fluent/fluentd:latest
    volumes:
      - cron-logs:/var/logs
      - ./fluentd/fluent.conf:/fluentd/etc/fluent.conf
    networks:
      - mynet

volumes:
  cron-logs:

networks:
  mynet:
    driver: bridge
5. How to Run the Project
Run the following commands in your terminal:
# Step 1: Build and start containers
docker-compose up --build

# Step 2: Check running containers
docker ps

# Step 3: Access Nginx
# Open your browser and go to:
http://localhost:8080

# Step 4: View logs written by cron
docker logs <cron-logger-container-id>

# Step 5: Check Fluentd output
docker logs <fluentd-container-id>
6. Docker Networking

A custom bridge network mynet is created to allow containers to communicate using container names.
ping cron-logger
ping nginx

7. Data Persistence Test
8. Stop and remove containers:
9. docker-compose down
Restart:
docker-compose up

Check that previous logs are still available:
cat ./cron-logger/logs/cron.log

Resource Limits
Each container has CPU and memory restrictions defined in docker-compose.yml:
deploy:
  resources:
    limits:
      cpus: '0.5'
      memory: 256M
9. Cleanup

To remove all containers, volumes, and networks:
docker-compose down -v
10. Validation Checklist

✅ Cron job logs written to /var/logs/cron.log
✅ Logs persist after container restart
✅ Fluentd prints log data to stdout
✅ Nginx serves index.html on port 8080
✅ All containers communicate via mynet
✅ Resource limits applied correctly





