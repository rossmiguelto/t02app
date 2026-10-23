# T02 Project 2 — Containerize and Run Locally

## Overview

This project demonstrates a multi-stage Docker build, Docker Compose services for a static website and Redis, and a serverless function executed locally with AWS SAM. No cloud deployment is required.

## Prerequisites

- Docker Desktop running with WSL integration enabled
- Git
- AWS SAM CLI
- Internet access for initial image downloads

## 1. Build the Docker image

Run from the project root:

    docker build -t t02app-web .

## 2. Start the services

    docker compose up -d --build

## 3. Confirm the services respond

    docker compose ps
    docker compose exec cache redis-cli ping
    curl -s -o /dev/null -w "%{http_code}\n" http://localhost:8080

Redis should return PONG and the website should return HTTP status 200.

## 4. Invoke the function locally

    cd local-fn
    sam local invoke HelloWorldFunction
    cd ..

The expected result contains status code 200 and the message hello world. The first invocation may take longer while SAM downloads its runtime image.

## 5. Stop the services

Run from the project root:

    docker compose down

## Project structure

- Dockerfile: multi-stage Node.js build and NGINX runtime
- Dockerfile.single: single-stage comparison Dockerfile
- compose.yaml: web and Redis services with a Redis health check
- local-fn/: AWS SAM Hello World function
- RATIONALE.md: design choices, measurements, and references
