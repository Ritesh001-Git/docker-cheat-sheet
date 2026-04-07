# 🐳 Docker Overlay File System (OverlayFS)

OverlayFS is a union filesystem used by Docker to combine multiple layers into a single unified view.
It allows Docker to efficiently manage image layers and container changes using a layered architecture.
OverlayFS is the magic that allows containers to be lightweight, fast, and disk-efficient.

👉 In simple terms:
Multiple read-only layers + one writable layer = one merged filesystem

https://github.com/Ritesh001-Git/docker-cheat-sheet/blob/main/overlay.jpeg

## 1. The Four-Layer Architecture
OverlayFS "overlays" two or more directories on top of each other and presents them as a single, unified directory to the user. Internally, it identifies four specific roles:

### 1. LowerDir (The Base)
These are your Read-Only image layers. In a typical Docker image, you might have 5–10 of these (one for the OS, one for Python, one for your app code).

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

### 2. UpperDir (The Diff)
This is the Writable layer. When you modify a file inside a running container, the changes are stored here.

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

### 3. MergedDir (The View)
This is the "unified" view. When you are inside the container running ls, you are looking at the MergedDir. It shows the combined contents of the Lower and Upper layers.

- Combines lower + upper layers
- This is what the container actually sees

👉 When you access: `/etc/config.txt`

Docker checks:

Upper layer → if exists, use it
Else → fallback to lower layer

📁 Example path: ``/var/lib/docker/overlay2/<container-id>/merged/`

👉 This gives the illusion of a single filesystem

### 4. WorkDir
An internal directory used by the Linux kernel to prepare files before they are moved to the MergedDir (atomic operations).

- Used internally by OverlayFS
- Required for copy-on-write operations

📁 Example path: `/var/lib/docker/overlay2/<container-id>/work/`

👉 You normally don’t interact with this

## 2. How Operations Work Internally
The true genius of OverlayFS is how it handles different file operations using a principle called Copy-on-Write (CoW).

### Reading a File
When a process inside the container tries to read a file:
- Case A: The file exists in the UpperDir (it was modified). Docker serves that version immediately.
- Case B: The file does not exist in the UpperDir. Docker looks down into the LowerDir layers. It finds the file and serves it.
- Result: Extremely fast. There is almost zero performance hit for reading.

### Modifying an Existing File (The "Copy-up" Operation)
This is where the magic happens. Since the LowerDir is read-only, Docker cannot change it.

- If you edit /etc/config.txt, OverlayFS searches the layers.
- It finds the file in a LowerDir.
- It performs a "Copy-up": It copies the entire file from the read-only layer into the UpperDir.
- The version in the UpperDir is modified.
- In the MergedDir, the new version "hides" the old version.

Implication: This is why writing to a container is slower than writing to a volume. The first time you edit a large file, there is a delay while it is copied up.

### Deleting a File (The "Whiteout")
You can't actually delete a file from a read-only LowerDir. So, how does Docker make it "disappear"?

- When you run rm file.txt, Docker creates a special character file called a Whiteout file in the UpperDir.
- When OverlayFS generates the MergedDir view, it sees the Whiteout file and knows to hide the corresponding file from the LowerDir.
- The file still exists in the image layers (consuming space), but it is invisible to the container.

⚙️ How It Works
Step-by-step:

- File exists in lower layer: `lowerdir → /etc/config.txt`
- You delete it inside container: `rm /etc/config.txt`
- Docker does NOT delete the actual file instead, it creates a whiteout file in upperdir: `upperdir → .wh.config.txt`
- During merge:
  OverlayFS sees `.wh.config.txt1`

  It hides `/etc/config.txt` from lower layer

## 3. Why This Architecture Matters
### Disk Efficiency
If you run 100 containers from the same Ubuntu image, you don't have 100 copies of Ubuntu. You have one set of LowerDir layers (read-only) shared by all 100 containers. Each container only consumes disk space for the UpperDir (the specific changes it makes).

### Fast Container Start
Because starting a container only requires creating a new, empty UpperDir and WorkDir and telling the kernel to "mount" them over the existing image layers, it happens in milliseconds.

### Security
Since the LowerDir layers are immutable (read-only), a process inside the container cannot corrupt the underlying image. Any "damage" is contained within the UpperDir, which is destroyed when the container is deleted.
