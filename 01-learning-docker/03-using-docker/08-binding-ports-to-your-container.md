# 08 – Binding Ports to Your Container

So far, our containers have only been useful “internally” (they run programs inside the container).
To make containers more useful, we often need to access a container service (like a web server) from the host machine.

Docker enables this using **port binding** (port mapping).

---

## Key Concept: Port Binding (Port Mapping)

Port binding allows Docker to:

- Take a port on your host machine
- Map it to a port inside the container

This makes container network services reachable from your computer (browser, API client, etc.).

### Format

-p <host_port>:<container_port>

A simple mnemonic:
**outside : inside**

- outside = host port
- inside = container port

---

## Exercise Files

03_08 % ls  
web-server.Dockerfile   web-server.bash

---

## web-server.Dockerfile (Full Content)

FROM ubuntu
LABEL maintainer="Carlos Nunez <dev@carlosnunez.me>"

USER root
COPY ./web-server.bash /

RUN chmod 755 /web-server.bash
RUN apt -y update
RUN apt -y install bash netcat-openbsd

USER nobody

ENTRYPOINT [ "/web-server.bash" ]

### Notes

- Installs `netcat-openbsd` because the server uses `nc` to listen on a port
- Runs as `nobody` for safer runtime behavior
- ENTRYPOINT runs `/web-server.bash` when the container starts

---

## web-server.bash (Full Content)

# !/usr/bin/env bash

start_server() {
  echo "Server started. Visit <http://localhost:5000> to use it."
  message=$(echo "<html><body><p>Hello! Today's date is $(date).</p></body></html>")
  length=$(wc -c <<< "$message")
  payload="HTTP/1.1 200 OK
Content-Length: $((length-1))

$message"
  while true
  do echo -ne "$payload" | nc -l -p 5000
  done
}

start_server

### What This Script Does

- Prints instructions: “Visit <http://localhost:5000”>
- Starts a minimal HTTP server using `netcat-openbsd`
- Listens on container port **5000**
- Responds with an HTML page containing the current date

---

## Step 1: Build the Image

### Command

```bash
docker build -t our-web-server -f web-server.Dockerfile .
```

### Explanation

- `-t our-web-server` tags the image name
- `-f web-server.Dockerfile` selects the Dockerfile
- `.` is the build context

---

## Step 2: Run the Container (No Port Binding Yet)

### Command

```bash
docker run -d --name our-web-server our-web-server
```

### Output

0a5b752846a929ea8a1b0d5b431e62d9ec3e11446a40e5f02b91b6a5a6fd2cde

### Explanation

- `-d` runs in detached mode
- `--name our-web-server` assigns a friendly container name
- Running in background means you’ll check output via logs

---

## Step 3: Confirm It’s Running

### Command

```bash
docker ps
```

### Output

CONTAINER ID   IMAGE            COMMAND              CREATED         STATUS         PORTS     NAMES
0a5b752846a9   our-web-server   "/web-server.bash"   3 seconds ago   Up 3 seconds             our-web-server

---

## Step 4: Read Logs for Instructions

### Command

```bash
docker logs our-web-server
```

### Output

Server started. Visit <http://localhost:5000> to use it.

### Why Accessing It Fails

Even though the server listens on port **5000 inside the container**:

- That port is **not exposed to the host**
- So `http://localhost:5000` on your machine will not reach it

You must bind a host port to the container port.

---

## Step 5: Stop + Remove Container Quickly

Instead of running `docker stop` then `docker rm`, you can do both in one command.

### Command

```bash
docker rm -f our-web-server
```

### Output

our-web-server

### Explanation

- `-f` force-stops the container if it’s running, then removes it

---

## Step 6: Run Again With Port Binding

### Command

```bash
docker run -d --name our-web-server -p 5001:5000 our-web-server
```

### Output

8a7a0f798c07604ba2c79ceb658540962e053c1d1da48e94fdf939455485509c

### Explanation

- Host port: **5001**
- Container port: **5000**
- Now requests sent to `localhost:5001` on your computer get forwarded to port 5000 inside the container

---

## Access From Browser

### URL

<http://localhost:5001/>

### Result

A webpage loads with content like:

Hello! Today's date is Sun Dec 28 16:42:02 UTC 2025.

---

## Key Takeaways

- Containers can run network services that listen on internal ports.
- Without port binding, those ports are not reachable from the host.
- Use `-p host:container` to bind ports.
- Remember: **outside : inside** (host port : container port).
- Naming containers (`--name`) makes `docker logs`, `docker rm`, etc. easier.
