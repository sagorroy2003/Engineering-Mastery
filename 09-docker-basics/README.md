# 🐳 09. Docker & Containerization Basics

![Difficulty: Advanced](https://img.shields.io/badge/Difficulty-Advanced-orange?style=for-the-badge)
![Focus: Containerization](https://img.shields.io/badge/Focus-Containerization-blue?style=for-the-badge)
![Status: Essential](https://img.shields.io/badge/Status-Essential-success?style=for-the-badge)

"It works on my machine" is an excuse that will instantly fail a technical interview at a top-tier agency. 

When you build a complex system like a multi-vendor marketplace, you have a specific version of Node.js, a specific version of PostgreSQL, and specific environment variables. If you try to manually install all of these on a production Linux server, you will eventually face version conflicts and deployment nightmares. Docker solves this by packaging your application and its entire environment into a standardized unit.

---

## 🏗️ The Architecture: Images vs. Containers

To understand Docker, you must understand the difference between its two core components.



* **Docker Image (The Blueprint):** A read-only template that contains your source code, the runtime (like Node.js), libraries, and environment variables. Think of this as a `Class` in Object-Oriented Programming.
* **Docker Container (The Instance):** A runnable instance of a Docker Image. Think of this as the `Object` instantiated from the Class. You can run 10 identical containers from a single image.

---

## 🔍 Code Review: Writing a Professional Dockerfile

A `Dockerfile` is a script containing instructions on how to build your image. Let's look at how to Dockerize a Node.js backend properly.

### ❌ The "Junior" Dockerfile (Bloated & Insecure)
```dockerfile
# 1. Uses the massive default Node image (1GB+)
FROM node:20

# 2. Copies everything, including local node_modules and .env files
COPY . .

# 3. Runs as the root user (Massive security risk)
RUN npm install
CMD ["node", "server.js"]
```

### ✅ The "Senior" Dockerfile (Multi-Stage & Hardened)
A professional build uses **Multi-Stage Builds**. We use a heavy image to compile the code (e.g., TypeScript to JavaScript), but we only copy the final, compiled output into a tiny, secure runtime image.



```dockerfile
# Stage 1: The Builder Environment
FROM node:20-alpine AS builder
WORKDIR /app
# Copy only package files first to leverage Docker's caching layer
COPY package*.json ./
RUN npm ci 
COPY . .
# Compile TypeScript/Babel to the /dist folder
RUN npm run build 

# Stage 2: The Production Runtime Environment
FROM node:20-alpine AS production
WORKDIR /app
# Only copy the production dependencies
COPY package*.json ./
RUN npm ci --omit=dev

# Copy only the compiled output from the builder stage
COPY --from=builder /app/dist ./dist

# Security: Do not run the container as the root user!
USER node 

# Expose the port your app runs on
EXPOSE 8080
CMD ["node", "dist/server.js"]
```
**The Trade-Off Analysis:** This approach is slightly more complex to write, but it reduces the final image size from ~1.2GB down to ~150MB, drastically speeds up deployment pipelines, and minimizes the attack surface.

---

## 🐙 Docker Compose: Orchestrating Microservices

A real application rarely lives in one container. Your Node.js API needs to talk to a PostgreSQL database and a Redis cache. **Docker Compose** allows you to define and run multi-container applications using a single `docker-compose.yml` file.

### Networking & Volumes
* **Networking:** Containers in the same `docker-compose` network can talk to each other using their service names as hostnames. Your backend doesn't connect to `localhost:5432`; it connects to `postgres:5432`.
* **Volumes:** Containers are ephemeral (temporary). If you restart a PostgreSQL container, all your marketplace data is destroyed. We use Volumes to map the container's internal data directory to a persistent folder on the host machine.

```yaml
# docker-compose.yml
version: '3.8'

services:
  api:
    build: 
      context: ./backend
      target: production
    ports:
      - "8080:8080"
    environment:
      - DB_HOST=postgres
      - DB_USER=unibazer_admin
      - DB_PASS=securepassword
    depends_on:
      - postgres

  postgres:
    image: postgres:15-alpine
    environment:
      POSTGRES_USER: unibazer_admin
      POSTGRES_PASSWORD: securepassword
      POSTGRES_DB: marketplace
    ports:
      - "5432:5432"
    volumes:
      # Maps local ./pgdata folder to the container's data directory
      - ./pgdata:/var/lib/postgresql/data 
```

---

## 🎯 Practical Exercises
1. **Dockerize a Script:** Write a simple C++ or Node.js script that prints "Hello from Docker". Write a Dockerfile for it, build the image (`docker build -t my-script .`), and run it (`docker run my-script`).
2. **Implement `.dockerignore`:** Create a `.dockerignore` file in your project. Ensure `node_modules`, `.git`, and `.env` are listed so they are never accidentally copied into your images.
3. **Spin up a Database:** Use `docker-compose` to spin up a PostgreSQL instance and pgAdmin (a web-based DB manager) on your local machine without installing either of them directly onto your operating system.

## 🎤 Interview Questions
* *"What is the difference between `npm install` and `npm ci` inside a Dockerfile?"*
* *"If you have a container running a database, and you forcefully delete the container using `docker rm -f`, what happens to your data? How do you prevent data loss?"*
* *"Walk me through the concept of Docker Layer Caching. Why do we copy `package.json` and run `npm install` before copying the rest of our source code?"*
