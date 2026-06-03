# NodeGoat Secure Deployment

## Project Overview

This project demonstrates a production-style Docker deployment of the OWASP NodeGoat application using Docker containers, MongoDB, Docker networking, persistent storage, and an NGINX reverse proxy.

The objective of this assignment was to containerize the application, configure networking between containers, implement persistent storage, expose the application securely using NGINX, and practice Docker monitoring and troubleshooting commands.

---

# Technologies Used

* Docker
* Docker Networking
* Docker Volumes
* MongoDB
* Node.js
* Express.js
* NGINX
* GitHub

---

# Repository Analysis

* Runtime: Node.js
* Framework: Express.js
* Default Port: 4000
* Database: MongoDB
* Reverse Proxy: NGINX
* Main Configuration File: `config/env/all.js`

The application uses MongoDB as the backend database and runs on port 4000 by default.

---

# Project Architecture

The deployment consists of three Docker containers:

1. MongoDB Database Container
2. NodeGoat Application Container
3. NGINX Reverse Proxy Container

All containers communicate through a custom Docker bridge network named:

```bash
nodegoat-network
```

Persistent storage for MongoDB is handled using a Docker volume named:

```bash
mongodb-data
```

---

# Docker Image Build

The Docker image was built using the following command:

```bash
docker build -t nodegoat-secure:v1 .
```

To verify the image:

```bash
docker images
```

---

# Docker Network Creation

A custom Docker network was created for secure inter-container communication:

```bash
docker network create nodegoat-network
```

This allows containers to communicate using container names instead of localhost.

---

# Docker Volume Creation

A persistent Docker volume was created for MongoDB data:

```bash
docker volume create mongodb-data
```

This ensures database data remains even if the MongoDB container is removed or restarted.

---

# MongoDB Container

The MongoDB container was created using:

```bash
docker run -d --name mongodb \
--network nodegoat-network \
-v mongodb-data:/data/db \
mongo:4.2
```

MongoDB runs internally on the Docker network and stores data inside the persistent volume.

---

# NodeGoat Application Container

The NodeGoat application container was started using:

```bash
docker run -d --name nodegoat-app \
--network nodegoat-network \
-p 4000:4000 \
-e MONGO_URL=mongodb://mongodb:27017/nodegoat \
nodegoat-secure:v1
```

The application successfully connects to MongoDB through the custom Docker network.

The application is accessible directly at:

```txt
http://localhost:4000
```

---

# NGINX Reverse Proxy

An NGINX reverse proxy container was configured to expose the application through port 80.

The reverse proxy forwards incoming traffic to the NodeGoat application container.

Example NGINX configuration:

```nginx
server {
    listen 80;
    server_tokens off;

    gzip on;

    add_header X-Frame-Options SAMEORIGIN;
    add_header X-Content-Type-Options nosniff;

    location / {
        proxy_pass http://nodegoat-app:4000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

The application is accessible through:

```txt
http://localhost
```

---

# Security Hardening

The following security improvements were implemented:

* Reverse proxy using NGINX
* Hidden NGINX server tokens
* Added security headers
* Docker network isolation
* Persistent storage using Docker volumes
* Production environment variables
* `.dockerignore` used to reduce unnecessary files
* MongoDB isolated within the internal Docker network

---

# Monitoring and Troubleshooting

The following Docker monitoring and troubleshooting commands were used:

```bash
docker ps
docker logs nodegoat-app
docker inspect nodegoat-app
docker exec -it nodegoat-app sh
docker top nodegoat-app
docker stats
docker network inspect nodegoat-network
docker volume inspect mongodb-data
```

These commands were used to verify container health, inspect networking, analyze logs, and validate persistent storage.

---

# Persistent Storage Validation

MongoDB persistent storage was validated using the `mongodb-data` Docker volume.

Even after restarting or recreating the MongoDB container, the database data remains intact because it is stored inside the Docker volume.

---

# Screenshots Included

This project includes screenshots demonstrating:

* Docker image build
* Docker containers running
* Docker network configuration
* Docker volume configuration
* NodeGoat application running
* NGINX reverse proxy working
* Monitoring and troubleshooting commands
* Container communication

---

# GitHub Repository

Repository Link:

```txt
https://github.com/bakhtiyark06/nodegoat-secure-deployment
```

---

# Conclusion

This project successfully demonstrates a production-style Docker deployment using container networking, persistent storage, reverse proxy integration, monitoring tools, and Docker security best practices.
