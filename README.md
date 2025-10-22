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
