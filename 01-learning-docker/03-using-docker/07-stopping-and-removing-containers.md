# 07 – Stopping and Removing Containers

By default, Docker does **not** stop or remove containers automatically.
Over time, this can leave many inactive containers on your system (cruft), which can:

- Waste disk space
- Use system resources (especially if still running)
- Make `docker ps` output noisy and harder to manage

This section covers:

- Stopping containers (`docker stop`)
- Removing containers (`docker rm`)
- Bulk cleanup using `docker ps -aq | xargs docker rm`
- Removing images (`docker rmi`)

---

## Listing Containers (Running + Stopped)

### Command

docker ps -a

### Output

CONTAINER ID   IMAGE               COMMAND              CREATED         STATUS                       PORTS     NAMES
6448220eca64   our-first-server    "/server.bash"       4 minutes ago   Up 4 minutes                           focused_galois
dfb4d2bcd593   our-first-server    "/server.bash"       7 minutes ago   Exited (137) 6 minutes ago             serene_buck
41f824ac191f   our-first-server    "/server.bash"       8 minutes ago   Exited (137) 5 minutes ago             objective_hamilton
3dec638a335e   our-first-server    "/server.bash"       8 minutes ago   Exited (137) 5 minutes ago             amazing_thompson
4eac10e0c81c   bf11154317dd        "/entrypoint.bash"   2 hours ago     Exited (0) 2 hours ago                 laughing_pasteur
d4cf73e36b8c   hello-world:linux   "/hello"             2 hours ago     Exited (0) 2 hours ago                 hopeful_rubin
194e24b7fcd1   hello-world:linux   "/hello"             2 hours ago     Exited (0) 2 hours ago                 flamboyant_haslett

### Notes

- `Up ...` means the container is still running.
- `Exited (0)` means success (normal exit).
- `Exited (137)` often indicates a forced stop (SIGKILL / kill).

---

## Stopping a Running Container (Graceful Stop)

### Command

```bash
docker stop 644
```

### Output

644

### Explanation

- `docker stop` attempts a **graceful shutdown**.
- Docker sends a stop signal and waits for the process to exit.
- This can take a few seconds (by default, Docker waits up to ~10 seconds).

---

## Force-Stopping a Container (Immediate Stop)

### Command

```bash
docker stop -t 0 <id>
```

### Explanation

- `-t 0` sets the timeout to 0 seconds.
- Docker stops the container immediately.
- Use carefully: forcing a stop can cause **data loss** for stateful apps (databases, queues, etc.).

---

## Confirming Container Status After Stop

### Command

```bash
docker ps -a
```

### Output

CONTAINER ID   IMAGE               COMMAND              CREATED          STATUS                            PORTS     NAMES
6448220eca64   our-first-server    "/server.bash"       12 minutes ago   Exited (137) About a minute ago             focused_galois
dfb4d2bcd593   our-first-server    "/server.bash"       14 minutes ago   Exited (137) 14 minutes ago                 serene_buck
41f824ac191f   our-first-server    "/server.bash"       15 minutes ago   Exited (137) 12 minutes ago                 objective_hamilton
3dec638a335e   our-first-server    "/server.bash"       16 minutes ago   Exited (137) 12 minutes ago                 amazing_thompson
4eac10e0c81c   bf11154317dd        "/entrypoint.bash"   2 hours ago      Exited (0) 2 hours ago                      laughing_pasteur
d4cf73e36b8c   hello-world:linux   "/hello"             2 hours ago      Exited (0) 2 hours ago                      hopeful_rubin
194e24b7fcd1   hello-world:linux   "/hello"             2 hours ago      Exited (0) 2 hours ago                      flamboyant_haslett

---

## Removing a Stopped Container

### Command

```bash
docker rm 6448220eca64
```

### Output

6448220eca64

### Explanation

- `docker rm` removes the container record from Docker.
- It only works on **stopped** containers.
- It does not remove the image.

---

## Verifying Removal

### Command

```bash
docker ps -a
```

### Output

CONTAINER ID   IMAGE               COMMAND              CREATED          STATUS                        PORTS     NAMES
dfb4d2bcd593   our-first-server    "/server.bash"       15 minutes ago   Exited (137) 14 minutes ago             serene_buck
41f824ac191f   our-first-server    "/server.bash"       16 minutes ago   Exited (137) 13 minutes ago             objective_hamilton
3dec638a335e   our-first-server    "/server.bash"       16 minutes ago   Exited (137) 13 minutes ago             amazing_thompson
4eac10e0c81c   bf11154317dd        "/entrypoint.bash"   2 hours ago      Exited (0) 2 hours ago                  laughing_pasteur
d4cf73e36b8c   hello-world:linux   "/hello"             2 hours ago      Exited (0) 2 hours ago                  hopeful_rubin
194e24b7fcd1   hello-world:linux   "/hello"             2 hours ago      Exited (0) 2 hours ago                  flamboyant_haslett

---

## Bulk Removing All Containers (Cleanup Trick)

Instead of removing containers one-by-one, you can remove all containers at once.

### Command

```bash
docker ps -aq | xargs docker rm
```

### What It Does

- `docker ps -a` lists all containers (running + stopped)
- `-q` makes it output **only IDs**
- `xargs docker rm` feeds each ID into `docker rm`

### Output

dfb4d2bcd593
41f824ac191f
3dec638a335e
4eac10e0c81c
d4cf73e36b8c
194e24b7fcd1

---

## Confirming All Containers Are Gone

### Command

```bash
docker ps -a
```

### Output

CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES

---

## Listing Images

Containers are removed, but images can still consume a lot of disk space.

### Command

```bash
docker images
```

### Output

REPOSITORY         TAG       IMAGE ID       CREATED          SIZE
our-first-server   latest    f2ec0f8cc840   20 minutes ago   242MB
our-first-image    latest    561e145f05ad   2 hours ago      258MB
hello-world        linux     53cc1017c16a   4 months ago     16.9kB

---

## Removing an Image

### Command

```bash
docker rmi our-first-server
```

### Output

Untagged: our-first-server:latest
Deleted: sha256:f2ec0f8cc840695c4950b1bcce309e6d218bd62080bf84846ed5691085919795

### Notes

- Docker removes the tag and deletes the image layers if not referenced elsewhere.
- If an image is being used by a running container, Docker may refuse to remove it.
  - Stop containers first, then retry.
- `docker rmi -f` can force removal, but may cause unpredictable behavior if misused.

---

## Confirming Image Removal

### Command

```bash
docker images
```

### Output

REPOSITORY        TAG       IMAGE ID       CREATED        SIZE
our-first-image   latest    561e145f05ad   2 hours ago    258MB
hello-world       linux     53cc1017c16a   4 months ago   16.9kB

---

## Key Takeaways

- Docker keeps containers unless you remove them.
- Use `docker stop` for graceful shutdown.
- Use `docker stop -t 0` for immediate shutdown (careful with stateful apps).
- Use `docker rm` to remove stopped containers.
- Use `docker ps -aq | xargs docker rm` to wipe all containers quickly.
- Use `docker rmi` to remove images and free disk space.
