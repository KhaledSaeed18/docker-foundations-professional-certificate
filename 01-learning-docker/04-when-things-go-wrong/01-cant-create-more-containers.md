# 01 - Help! I Can’t Seem to Create More Containers

At some point, almost everyone who uses Docker long enough will hit a confusing error:
> Docker claims there’s **no disk space left**, even though your system clearly has plenty of free space.

This section explains **why this happens** and how to fix it safely.

---

## The Problem: “No Space Left on Device”

You may see errors when:

- Building images (e.g. OpenJDK, large base images)
- Creating new containers
- Pulling images from Docker Hub

Even after running:
df -h

…you still see plenty of free disk space on your machine.

So what’s going on?

---

## Why This Happens

Docker images are made of **compressed layers**.
Those layers must live somewhere on disk.

On:

- **macOS**
- **Some Windows setups**

Docker does **not** run directly on the host OS.  
Instead, the Docker Engine runs inside a **small virtual machine**.

What actually ran out of space is:

- Docker’s internal storage inside that VM
- Not your main system disk

The fix is usually simple — no VM hacking required.

---

## Step 1: Check Existing Images

### Command

docker images

### Why

You may have:

- Old images
- Large images
- Images you no longer use

All of these consume space inside Docker’s storage.

---

## Step 2: Remove Unused Images

Docker lets you remove **multiple images at once**.

### Command

docker rmi <image1> <image2> <image3>

Example (conceptual):
docker rmi  nginx selenium/standalone-chrome selenium/standalone-firefox

### Notes

- Images are deleted immediately
- You may need to stop containers using these images first:
  - docker rm <container>
- You can force removal with:
  docker rmi -f <image>

⚠️ Be careful with `-f` — force removal can cause data loss if containers depend on the image.

---

## Step 3: Reclaim Space Automatically (Recommended)

Docker provides a powerful cleanup command.

### Command

```bash
docker system prune
```

### What It Removes

- Stopped containers
- Unused networks
- Dangling images
- Unused build cache / intermediate layers

Docker will show a warning explaining what will be removed.

### Confirmation

- Press `y` → proceed
- Press `N` → abort

In real-world usage, this command can reclaim:

- Hundreds of MB
- Sometimes **multiple gigabytes** of space

This is one of the most useful Docker maintenance commands.

---

## Step 4: Try Again

After running:

- docker rmi
- docker system prune

Retry the command that failed earlier (e.g. docker run, docker build).

In most cases, it will now succeed without issues.

---

## Key Takeaways

- Docker can run out of space even when your system disk is fine
- On macOS/Windows, Docker stores data inside a VM
- Old images and build cache are common causes
- Use:
  - docker rmi → remove unused images
  - docker system prune → clean Docker storage safely
- This is a normal issue — every Docker user hits it eventually
