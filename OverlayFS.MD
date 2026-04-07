# 🐳 Docker Overlay File System (OverlayFS)

OverlayFS is a union filesystem used by Docker to combine multiple layers into a single unified view.
It allows Docker to efficiently manage image layers and container changes using a layered architecture.
OverlayFS is the magic that allows containers to be lightweight, fast, and disk-efficient.

👉 In simple terms:
Multiple read-only layers + one writable layer = one merged filesystem

## 1. The Four-Layer Architecture
OverlayFS "overlays" two or more directories on top of each other and presents them as a single, unified directory to the user. Internally, it identifies four specific roles:

### 1. LowerDir (The Base): These are your Read-Only image layers. In a typical Docker image, you might have 5–10 of these (one for the OS, one for Python, one for your app code).

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

### 2. UpperDir (The Diff): This is the Writable layer. When you modify a file inside a running container, the changes are stored here.

- Created when container runs
= This is the only writable layer
= All runtime changes go here
Example:
```
Creating files
Modifying config (/etc/config.txt)
Writing logs
```

📁 Example path: `/var/lib/docker/overlay2/<container-id>/diff/`

👉 Important:

Does NOT modify lower layers
Stores only changes (delta)

### 3. MergedDir (The View): This is the "unified" view. When you are inside the container running ls, you are looking at the MergedDir. It shows the combined contents of the Lower and Upper layers.

- Combines lower + upper layers
- This is what the container actually sees

👉 When you access: `/etc/config.txt`

Docker checks:

Upper layer → if exists, use it
Else → fallback to lower layer

📁 Example path: ``/var/lib/docker/overlay2/<container-id>/merged/`

👉 This gives the illusion of a single filesystem

### 4. WorkDir: An internal directory used by the Linux kernel to prepare files before they are moved to the MergedDir (atomic operations).

- Used internally by OverlayFS
- Required for copy-on-write operations

📁 Example path: `/var/lib/docker/overlay2/<container-id>/work/`

👉 You normally don’t interact with this
