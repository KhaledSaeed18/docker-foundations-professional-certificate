# 06 – Interact with Your Container

Up to this point, the containers we ran exited immediately after executing their entrypoint.
This section focuses on **long-running containers** (like servers) and how to interact with them while they run.

---

## Exercise Files

Directory contents:

3_06 % ls  
Dockerfile              entrypoint.bash         server.Dockerfile       server.bash

This section introduces two new files:

- `server.Dockerfile`
- `server.bash`

---

## server.Dockerfile (Full Content)

FROM ubuntu
LABEL maintainer="Carlos Nunez <dev@carlosnunez.me>"

USER root
COPY ./server.bash /

RUN chmod 755 /server.bash
RUN apt -y update
RUN apt -y install bash

USER nobody

ENTRYPOINT [ "/server.bash" ]

---

## server.bash (Full Content)

# !/usr/bin/env bash

bash_is_current_version() {
  bash --version | grep -q 'version 5'
}

start_server() {
  echo "Server started. Press CTRL-C to stop..."
  while true
  do sleep 10
  done
}

if ! bash_is_current_version
then
  >&2 echo "ERROR: Bash not installed or not the right version."
  exit 1
fi

start_server

---

## What’s Different About This Container?

The script `server.bash` starts a server-like process:

- It prints a startup message
- Then it loops forever (until stopped)

This creates a **long-running container**, unlike `hello-world` which exits immediately.

---

## Building an Image from a Non-Default Dockerfile

Because the Dockerfile is named `server.Dockerfile` (not `Dockerfile`), we must specify it using `--file`.

### Command

```bash
docker build --file server.Dockerfile --tag our-first-server .
```

### What This Command Means

- `--file server.Dockerfile` tells Docker which Dockerfile to use
- `--tag our-first-server` assigns a friendly image name
- `.` is the context (current directory)

---

## Running a Long-Running Container in the Foreground

### Command

```bash
docker run our-first-server
```

### Output

Server started. Press CTRL-C to stop...

### What Happened

- `docker run` created + started the container
- It also **attached your terminal** to the container output
- Because the script loops forever, the terminal appears “stuck” (it’s just attached)

---

## Stopping a Running Container from Another Terminal

### Command

```bash
docker ps
```

(Used to find the container ID.)

Then:

```bash
docker kill dfb
```

### Output

dfb

### Explanation

- `docker kill` force-stops a running container
- Docker prints the container ID as confirmation
- The container stops, and the first terminal is released

---

## Running the Container in Detached Mode

Detached mode runs the container in the background (no terminal attach).

### Command

```bash
docker run -d our-first-server
```

### Output

6448220eca64d7584457ad3ddedf0c9ea27d4013b77fe853497591df9a5d8ffa

### Explanation

- `-d` starts the container in the background
- Docker returns the container ID immediately

---

## Confirming the Container is Running

### Command

```bash
docker ps
```

### Output

CONTAINER ID   IMAGE              COMMAND          CREATED          STATUS          PORTS     NAMES  
6448220eca64   our-first-server   "/server.bash"   26 seconds ago   Up 25 seconds             focused_galois

---

## Running Commands Inside a Running Container (`docker exec`)

`docker exec` runs a command **inside** a running container.

### Example: Run `date` inside the container

#### Command

```bash
docker exec 644 date
```

#### Output

Sun Dec 28 16:09:10 UTC 2025

### Why This Is Useful

- Troubleshooting
- Inspecting runtime environment
- Verifying tools/config inside the container

---

## Starting an Interactive Shell Inside the Container

To interactively type commands inside the container, you need:

- `--interactive` to keep STDIN open
- `--tty` to allocate a pseudo-terminal

### Command

```bash
docker exec --interactive --tty 644 bash
```

### Output / Session

nobody@6448220eca64:/$ exit  
exit

### Explanation

- You are now “inside” the container
- The prompt shows the container user (`nobody`) and container hostname/id
- `exit` leaves the container shell and returns to your real terminal

---

## Key Takeaways

- Long-running containers (servers) won’t exit on their own
- `docker run` attaches your terminal by default (foreground mode)
- Use `docker kill` to force-stop stuck/attached containers
- Use `docker run -d` to run containers in the background (detached)
- Use `docker exec <id> <command>` to run commands inside a running container
- Use `docker exec -it <id> bash` to open an interactive shell for debugging
