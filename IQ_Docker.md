# What is a Docker Container?
A running instance of a Docker image.

---

# What is a Docker Image?
A read-only template that contains application code, runtime, libraries, and dependencies used to create containers.

---

# What is Docker Compose?
A tool to define and run multi-container applications using a docker-compose.yml file.

---

# What is Docker networking?
Allows containers to communicate using:  
* bridge (default)
* host
* none
* overlay

---

# What is Docker networking?
  Docker networking is the system that enables containers to communicate with each other, with the host machine, and with external networks (like the internet).  
Because containers are isolated by design, Docker provides built-in networking features to control how they connect and share data securely and efficiently.

---

# Why Docker Networking Matters?
When you run multiple containers (for example, a web app + database), they need a way to:  
* Talk to each other
* Expose services to the outside world
* Stay isolated when necessary
* Control traffic flow
* Docker networking handles all of this.

---

# Docker Image Layers
Docker images are built in layers.
Each instruction creates a new layer.
```dockerfile
FROM node:18
WORKDIR /app

COPY package.json package-lock.json ./ (Recommended)
RUN npm install

CMD ["node", "app.js"]
```

# 📦 Layer Visualization

```
Layer 1 → Base image (Node)
Layer 2 → WORKDIR
Layer 3 → package.json
Layer 4 → npm install
Layer 5 → App source code
```

If Layer 3 changes → Layers 4 & 5 rebuild  
If only Layer 5 changes → Only last layer rebuilds  

---
Key Interview Concepts
* Docker uses Union File System
* Layers are immutable
* Containers add a read-write layer on top
* Images are built from stacked read-only layers
---

# 🧩 Quick Summary

| Instruction | Purpose                            |
| ----------- | ---------------------------------- |
| `EXPOSE`    | Dockerfile instruction that documents which network ports a container listens on at runtime.|
| `ENTRYPOINT`| defines the main command that always runs when a container starts. |
| `WORKDIR`   | Sets working directory             |
| `COPY`      | Copies files (preferred)           |
| `ADD`       | Copy + extract + URL support       |
| Layers      | Each instruction = new image layer |
| Cache       | Speeds up rebuilds                 |
