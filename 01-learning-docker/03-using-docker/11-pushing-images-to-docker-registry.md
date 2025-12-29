# 11 – Pushing Images to the Docker Registry

This section explains how to **push Docker images to Docker Hub** using the Docker CLI.

---

## Prerequisite

Before pushing images:

- You must have a Docker Hub account
- You must be logged in via the Docker CLI

---

## Step 1: Log In to Docker Hub

### Command

```bash
docker login
```

### What Happens

- Docker prompts for your Docker Hub username and password
- If authentication succeeds:
  - Docker confirms login success
  - Credentials are stored locally for future use

This allows Docker to authenticate when pushing or pulling private images.

---

## Why Tagging Is Required Before Pushing

Docker images must be named in a specific format before they can be pushed to a registry.

### Required Format

username/image-name:tag

Docker uses the **username prefix** to determine:

- Which registry namespace the image belongs to
- Which account owns the image

Without this format, Docker does not know where to push the image.

---

## Step 2: Tag the Image for Docker Hub

### Command

```bash
docker tag our-web-server khaled189/our-web-server:0.0.1
```

### Explanation

- `docker tag` renames an image (similar to `mv` for files)
- `our-web-server` is the local image name
- `khaled189` is my Docker Hub username
- `our-web-server` is the repository name on Docker Hub
- `0.0.1` is the image version tag

If no tag is specified, Docker defaults to `latest`.
Using explicit version tags avoids confusion and improves version control.

### Result

- The same image now has **two names**:
  - our-web-server
  - khaled189/our-web-server:0.0.1

No output indicates the command succeeded.

---

## Step 3: Push the Image to Docker Hub

### Command

```bash
docker push khaled189/our-web-server:0.0.1
```

### What Happens

- Docker uploads the image layers to Docker Hub
- Layers already present in the registry are skipped
- Progress output is similar to `docker pull`, but in reverse

Once complete:

- The image is available in your Docker Hub repository
- It can be pulled from any machine using Docker

---

## Key Takeaways

- Docker Hub requires authentication to push images
- Images must be tagged with `username/image:tag`
- `docker tag` renames images without duplicating data
- Versioned tags (e.g. `0.0.1`) are strongly recommended
- `docker push` uploads images to the registry for sharing and reuse
