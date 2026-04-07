# 🐳 Docker Overlay File System (OverlayFS)

OverlayFS is a union filesystem used by Docker to combine multiple layers into a single unified view.
It allows Docker to efficiently manage image layers and container changes using a layered architecture.
OverlayFS is the magic that allows containers to be lightweight, fast, and disk-efficient.

👉 In simple terms:
Multiple read-only layers + one writable layer = one merged filesystem

### 1. The Four-Layer Architecture
OverlayFS "overlays" two or more directories on top of each other and presents them as a single, unified directory to the user. Internally, it identifies four specific roles:

#### 1. LowerDir (The Base): These are your Read-Only image layers. In a typical Docker image, you might have 5–10 of these (one for the OS, one for Python, one for your app code).

- Contains image layers
- These layers are immutable (read-only)
- Created during Docker build (FROM, RUN, COPY)
Example:
``` 
Base image → ubuntu
Installed packages → RUN apt install
App code → COPY app.js 
```

📁 Example path: `/var/lib/docker/overlay2/<layer-id>/diff/`
