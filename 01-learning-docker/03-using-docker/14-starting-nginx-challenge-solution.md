# 14 – Challenge Solution: Starting NGINX (Serve a Static Website)

This challenge is about serving a static website using **NGINX in Docker**, using the official **nginx** image from Docker Hub.

Key point: because we use the existing NGINX image, **we do not need to write a Dockerfile**.

---

## Exercise Files

03_14_before % ls  
web-server.Dockerfile   web-server.bash         website

For this challenge, the important piece is the `website/` folder (the static site content).

---

## Requirements (From the Challenge Prompt)

We need to:

1. Use the **nginx** image from Docker Hub
2. Name the container: `website`
3. Mount the local `website/` folder into the container’s web root:
   - Container path: /usr/share/nginx/html
4. Make the website accessible from the host:
   - Host URL: <http://localhost:8080>
   - NGINX serves internally on port 80
5. Ensure the container removes itself when stopped:
   - Use `--rm`
6. Verify the container is gone afterward (`docker ps -a`)

---

## Key Docker Features Used

### 1) Container name

- `--name website`

### 2) Volume mount (static website files)

- `-v <host_path>:<container_path>`
- Mnemonic: **outside : inside**
  - outside = your machine
  - inside = inside the container

For this challenge:

- Host path: $PWD/website
- Container path: /usr/share/nginx/html

### 3) Port binding

- `-p <host_port>:<container_port>`
- Mnemonic: **outside : inside**

For this challenge:

- Host port: 8080
- Container port: 80

### 4) Auto-remove the container

- `--rm`
- Removes the container automatically after it stops (prevents cruft).

---

## Final Command (Solution)

```bash
docker run --name website -v $PWD/website:/usr/share/nginx/html -p 8080:80 --rm nginx
```

### What This Does

- Starts an NGINX container
- Names it `website`
- Serves your local `website/` folder via NGINX
- Exposes it on your host at port 8080
- Removes the container automatically when it stops

---

## Open the Website

In your browser, visit:

<http://localhost:8080>

You should see the static site (the “mission accomplished / congratulations” page in the challenge).

---

## Stopping the Container

In the terminal where NGINX is running:

- Press CTRL + C

Because we used `--rm`, stopping it will also remove it.

---

## Verify the Container Was Removed

### Command

```bash
docker ps -a
```

### What You Should See

- The `website` container should NOT appear.
- Important: `-a` is required, because stopped containers don’t show up in `docker ps` by default.

---

## Common Pitfall: Volume Path Parsing (Quote Fix)

Sometimes, depending on your shell/path, Docker may mis-parse the volume mount.
If you get a weird mount error, wrap the mount in quotes:

docker run --name website -v "$PWD/website:/usr/share/nginx/html" -p 8080:80 --rm nginx

---

## Key Takeaways

- Official images (like `nginx`) let you run real services without writing a Dockerfile.
- Use `-v` to supply website content.
- Use `-p` to expose container ports to your host.
- Use `--rm` to keep your system clean (containers auto-delete after stopping).
