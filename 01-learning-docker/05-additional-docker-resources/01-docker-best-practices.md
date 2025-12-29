# Docker Best Practices

Docker is powerful and flexible, but using it safely and predictably requires a few important habits. This section summarizes **three core best practices** you should apply as you move your applications into Docker, especially in real-world and production environments.

---

## 1. Be Careful with Images from Docker Hub

Docker Hub makes it incredibly easy to download and share images, but that convenience comes with risk.

### The Risk

Not every image on Docker Hub is safe. Malicious images have been uploaded in the past that:

- Steal credentials
- Run crypto miners
- Abuse network resources (which can become very expensive on cloud platforms)

A real example reported by Trend Micro in 2020 involved an image called **Alpine2**, which looked legitimate but secretly ran a crypto miner on startup.

### Best Practices

- Prefer **Official / Verified Images** on Docker Hub  
  These are scanned and vetted by Docker.
- Popular examples include:
  - `ubuntu`
  - `nginx`
  - `node`
- For unverified images, **scan before using**.

### Image Scanning Tools

Image scanners inspect each image layer for known vulnerabilities or malicious files.

Popular options:

- **Clair**
- **Trivy**
- **Dagda**

Both open‑source and paid scanners exist, and many CI/CD platforms integrate them automatically.

---

## 2. Always Use Explicit Image Version Tags

By default, Docker assigns the `latest` tag if no version is specified. This is convenient—but dangerous.

### Why `latest` Is a Problem

- You don’t know **which version** you’re actually running
- The image can **change silently** when pulled later
- Rollbacks become difficult or impossible
- Production systems can break unexpectedly

### Best Practice

Always tag images with explicit versions:

```bash
docker build -t my-app:1.0.0 .
```

Even for local development, versioning:

- Improves clarity
- Prevents surprises
- Makes debugging and rollback much easier

---

## 3. Avoid Running Containers as Root

By default, Docker containers run as the **root user**, which is a security risk.

### Why This Matters

- A compromised container running as root can:
  - Modify system files
  - Escape the container in worst-case scenarios
- Security teams strongly discourage root containers

### How to Fix This

#### Option 1: Specify a User at Runtime

For existing images:

```bash
docker run --user 1000 my-image
```

You can use:

- User IDs (recommended)
- Usernames (if defined in the image)

#### Option 2: Define a User in Your Dockerfile

For your own images:

```dockerfile
USER root
# install dependencies

USER nobody
# run application
```

This allows:

- Root access only where required
- Safer execution by default

⚠️ Note: Some images assume root access. You may need extra configuration when switching users.

---

## Final Thoughts

These best practices will:

- Improve security
- Prevent costly mistakes
- Make your Docker usage predictable and professional

Docker rewards discipline. A little extra care up front saves **hours of debugging and serious risk later**.
