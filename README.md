# NodeGoat Secure Deployment

## Project Overview

This project demonstrates a secure production-style deployment of the OWASP NodeGoat application using Docker containers, MongoDB, Docker networking, persistent volumes, and an NGINX reverse proxy.

The goal of this project was to containerize the application, configure inter-container communication, and expose the application securely through NGINX.

---

# Technologies Used

* Docker
* Docker Networks
* Docker Volumes
* MongoDB
* Node.js
* NGINX
* GitHub

---

# Project Architecture

The deployment consists of three main containers:

1. MongoDB Database Container
2. NodeGoat Application Container
3. NGINX Reverse Proxy Container

All containers communicate through a custom Docker bridge network called:

```bash
nodegoat-network
```

Persistent database storage is handled using a Docker volume:

```bash
mongodb-data
```

---

# Docker Image Build

The NodeGoat application image was built using the following command:

```bash
docker build -t nodegoat-secure:v1 .
```

---

# Docker Network Creation

```bash
docker network create nodegoat-network
```

---

# Docker Volume Creation

```bash
docker volume create mongodb-data
```

---

# MongoDB Container

```bash
docker run -d --name mongodb \
--network nodegoat-network \
-v mongodb-data:/data/db \
mongo:4.2
```

---

# NodeGoat Application Container

```bash
docker run -d --name nodegoat-app \
--network nodegoat-network \
-p 4000:4000 \
-e MONGO_URL=mongodb://mongodb:27017/nodegoat \
nodegoat-secure:v1
```

---

# NGINX Reverse Proxy

An NGINX reverse proxy was configured to securely expose the application on port 80.

The reverse proxy forwards requests to the NodeGoat application container running on port 4000.

Example configuration:

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

---

# Monitoring and Troubleshooting Commands

The following Docker commands were used during deployment and troubleshooting:

```bash
docker ps
docker logs nodegoat-app
docker inspect nodegoat-app
docker network inspect nodegoat-network
docker volume inspect mongodb-data
docker stats
```

---

# Screenshots Included

The project includes screenshots demonstrating:

* Docker containers running
* Docker networking
* Docker volumes
* NGINX reverse proxy configuration
* NodeGoat application running on localhost
* Monitoring and troubleshooting commands

---

# GitHub Repository

Repository Link:

```txt
https://github.com/bakhtiyark06/nodegoat-secure-deployment
```

---

# Conclusion

This project successfully demonstrates containerized application deployment using Docker with persistent storage, networking, reverse proxy configuration, and monitoring tools in a production-style environment.
