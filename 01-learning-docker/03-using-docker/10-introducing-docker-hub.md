# 10 – Introducing Docker Hub

Docker makes it easy to **share container images** by pushing them to container image registries.
This section introduces **Docker Hub**, the default and most widely used registry.

---

## What Is a Container Image Registry?

A **container image registry** is a service used to:

- Store container images
- Track different versions of images
- Download images when needed

Images in a registry are identified using **tags**.

---

## Image Names and Tags

An image reference typically looks like this:

image-name:tag

Examples:

- hello-world:linux
- ubuntu:latest
- our-web-server:1.0

### Tags Explained

- Tags usually represent **versions**
- If no tag is specified, Docker automatically uses:
  - `latest`

Example:

- `docker pull ubuntu`
  - Actually pulls `ubuntu:latest`

This versioning system makes it easy to:

- Pull specific versions
- Upgrade or downgrade images safely

---

## What Is Docker Hub?

**Docker Hub** is:

- Docker’s default container image registry
- Publicly accessible
- Integrated directly into the Docker CLI

By default:

- `docker pull` pulls images from Docker Hub
- `FROM ubuntu` in a Dockerfile pulls from Docker Hub
- `docker push` pushes images to Docker Hub (after login)

---

## Key Takeaways

- Docker Hub is Docker’s default image registry
- Images are identified using `name:tag`
- `latest` is the default tag if none is provided
- Docker automatically pulls base images from Docker Hub
- Pushing images enables sharing and reuse
