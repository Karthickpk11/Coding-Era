

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
| `WORKDIR`   | Sets working directory             |
| `COPY`      | Copies files (preferred)           |
| `ADD`       | Copy + extract + URL support       |
| Layers      | Each instruction = new image layer |
| Cache       | Speeds up rebuilds                 |
