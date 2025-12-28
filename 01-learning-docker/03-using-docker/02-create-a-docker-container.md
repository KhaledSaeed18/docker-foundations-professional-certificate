# 02 – Create a Docker Container (Long Way)

This section explains how Docker containers are created step by step using the Docker CLI.
The focus here is on **understanding what Docker does**, not on shortcuts.

---

## Containers and Images

A **Docker container** is created from a **Docker image**.

A Docker image is:

- A pre-packaged, compressed filesystem
- Contains the application
- Contains its environment and configuration
- Includes instructions on how to start the application

The instruction that tells Docker how to start the app is called the **entry point**.

---

## Creating a Container (Overview)

Creating a container involves these steps:

1. Choose an image
2. Pull the image if it does not exist locally
3. Create a container from that image
4. Start the container
5. Inspect its output or logs

The command used to create a container is:

```bash
docker container create
```

---

## Inspecting the Create Command

### Command

```bash
docker container create --help
```

### Purpose

Displays:

- Command usage
- Required arguments
- Optional flags

### Usage Output

Usage:  docker container create [OPTIONS] IMAGE [COMMAND] [ARG...]

Key points:

- **IMAGE** is required
- COMMAND and ARG are optional
- OPTIONS modify container behavior

---

## Creating a Container from an Image

### Command

```bash
docker container create hello-world:linux
```

### Explanation

- `hello-world` is the image name
- `linux` is the image tag
- The tag specifies a specific variant of the image

If the image does not exist locally:

- Docker pulls it from **Docker Hub** by default

### Output

Unable to find image 'hello-world:linux' locally  
linux: Pulling from library/hello-world  
198f93fd5094: Pull complete  
Digest: sha256:53cc1017c16ab2500aa5b5367e7650dbe2f753651d88792af1b522e5af328352  
Status: Downloaded newer image for hello-world:linux  
194e24b7fcd198a8170976a161fc81fb2b9eb2311d6435420e2f38fd8a102bc6  

### Key Observations

- Docker successfully pulled the image
- Docker returned a **container ID**
- The container was created but **not started**

---

## Listing Containers

### Command

```bash
docker ps
```

### Output

CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES

### Explanation

- Shows only **running containers**
- The hello-world container is not shown because it has not been started

---

### Command

```bash
docker ps --all
```

### Output

CONTAINER ID   IMAGE               COMMAND    CREATED         STATUS    PORTS     NAMES  
194e24b7fcd1   hello-world:linux   "/hello"   2 minutes ago   Created      -       flamboyant_haslett  

### Explanation

- `--all` shows **all containers**
- Status is **Created**
- The container exists but has never been executed

---

## Starting the Container

### Command

```bash
docker container start 194e24b7fcd198a8170976a161fc81fb2b9eb2311d6435420e2f38fd8a102bc6
```

### Output

194e24b7fcd198a8170976a161fc81fb2b9eb2311d6435420e2f38fd8a102bc6

### Explanation

- Docker started the container successfully
- The container executed its entry point
- No output is shown yet because it was not attached to the terminal

---

## Checking Container Status After Execution

### Command

```bash
docker ps --all
```

### Output

CONTAINER ID   IMAGE               COMMAND    CREATED         STATUS                      PORTS     NAMES  
194e24b7fcd1   hello-world:linux   "/hello"   3 minutes ago   Exited (0) 23 seconds ago    -         flamboyant_haslett  

### Explanation

- Status changed to **Exited (0)**
- Exit code `0` means the container ran successfully
- Non-zero exit codes indicate errors

---

## Viewing Container Logs

### Command

```bash
docker logs 194
```

Only the first 3 characters of the container ID are required.

### Output

Hello from Docker!  
This message shows that your installation appears to be working correctly.

Docker execution steps:

1. Docker client contacted the Docker daemon
2. Docker daemon pulled the image from Docker Hub
3. Docker daemon created a container
4. Docker daemon streamed output back to the client

### Why Logs Matter

- Useful for debugging failed containers
- Shows output from short-lived containers
- Essential when containers exit quickly

---

## Starting and Attaching to a Container

### Command

```bash
docker container start --attach 194
```

### Output

Hello from Docker!  
This message shows that your installation appears to be working correctly.

### Explanation

- Starts the container
- Attaches terminal directly to container output
- Eliminates the need to use `docker logs`

---

## Reusing Containers

A container:

- Is **not deleted automatically**
- Can be started multiple times
- Remains available until explicitly removed

This allows repeated execution without recreating the container.

---

## Key Takeaways

- Containers are created from images
- `docker container create` does **not** start containers
- Images are pulled automatically if missing
- Containers have unique IDs
- Exit code `0` indicates success
- Logs and attach are primary ways to see output

This section demonstrates the **long way** to understand what Docker does internally.
