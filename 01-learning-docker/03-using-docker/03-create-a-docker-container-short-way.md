# 03 – Create a Docker Container (Short Way)

This section introduces **docker run** — the shortcut command that bundles multiple container lifecycle steps into one.

---

## Why the “Short Way” Exists

Doing container creation manually every time would be inconvenient because you would need to:

- Create a container
- Start it
- Attach to see output
- Use `docker ps` to confirm status
- Use `docker logs` to view output afterward

Docker provides a simpler command that rolls these steps into one:

**docker run**

---

## What `docker run` Does

You can think of `docker run` as the combination of:

docker run = docker container create + docker container start + docker container attach

Meaning `docker run` will:

1. Create a container from an image
2. Start the container
3. Attach your terminal to its output (by default for this use case)

---

## Running the Hello World Container

### Command

```bash
docker run hello-world:linux
```

### Explanation

- Uses the `hello-world` image
- Uses the `linux` image tag
- If the image is not local, Docker pulls it from Docker Hub
- Runs the container immediately and prints output

### Output

Hello from Docker!  
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:

 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (arm64v8)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 <https://hub.docker.com/>

For more examples and ideas, visit:
 <https://docs.docker.com/get-started/>

### Key Observations

- Output appears immediately (because Docker attaches automatically)
- Unlike `docker container create`, this command **runs** the container right away
- `docker run` does not display the container ID by default

---

## Checking Container Results with `docker ps`

### Command

```bash
docker ps --all
```

### Output

CONTAINER ID   IMAGE               COMMAND    CREATED          STATUS                      PORTS     NAMES  
d4cf73e36b8c   hello-world:linux   "/hello"   59 seconds ago   Exited (0) 58 seconds ago     -       hopeful_rubin  
194e24b7fcd1   hello-world:linux   "/hello"   15 minutes ago   Exited (0) 8 minutes ago      -  flamboyant_haslett  

### Explanation

- `docker ps --all` shows both running and stopped containers
- The container created by `docker run` appears with status **Exited (0)**
- Exit code `0` indicates the container ran successfully

---

## Viewing Output Again with `docker logs`

Even though `docker run` prints output immediately, you can still retrieve output later using logs.

### Command

```bash
docker logs d4c
```

(Using the first 3 characters of the container ID is enough.)

### Output

Hello from Docker!  
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:

 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (arm64v8)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 <https://hub.docker.com/>

For more examples and ideas, visit:
 <https://docs.docker.com/get-started/>

---

## Key Takeaways

- `docker run` is the shortcut for quickly running containers
- It combines: create + start + attach (for this use case)
- It does not show container IDs; use `docker ps` to find them
- You can still inspect status and exit codes using `docker ps --all`
- You can still retrieve output using `docker logs`
