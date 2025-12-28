# 04 – Create a Docker Container from Dockerfiles

This section combines:

1) Understanding the Dockerfile and the exercise app  
2) Building an image from the Dockerfile and running a container from it

---

## Goal

So far, containers were created from images on Docker Hub.  
To containerize **your own app**, you need to:

- Write a **Dockerfile**
- Build a **Docker image** from it
- Run a **container** from that image

---

## Exercise Files

Directory contents:

03_05 % ls  
Dockerfile      entrypoint.bash

---

## Dockerfile (Full Content)

FROM ubuntu

LABEL maintainer="Carlos Nunez <dev@carlosnunez.me>"

USER root

COPY ./entrypoint.bash /

RUN apt -y update
RUN apt -y install curl bash
RUN chmod 755 /entrypoint.bash

USER nobody

ENTRYPOINT [ "/entrypoint.bash" ]

---

## entrypoint.bash (Full Content)

# !/usr/bin/env bash

bash_is_current_version() {
  bash --version | grep -q 'version 5'
}

curl_is_installed() {
  &>/dev/null which curl &&
    curl --version | grep -q '^curl'
}

get_current_date() {
  curl --silent --insecure -I google.com |
    grep -E '^Date' |
    sed 's/^Date: //'
      xargs -I {} date --date='{}'
}

if ! bash_is_current_version
then
  >&2 echo "ERROR: Bash not installed or not the right version."
  exit 1
fi

if ! curl_is_installed
then
  >&2 echo "ERROR: Curl is not installed."
  exit 1
fi

echo "Hello! The current date and time is $(get_current_date)"

---

## Understanding the Dockerfile Keywords

### FROM

- Chooses the **base image** to build on.
- Here, it uses: ubuntu
- If the base image is not local, Docker pulls it (usually from Docker Hub).

### LABEL

- Adds metadata to the image.
- Here it records a maintainer label.

### USER

- Sets which user future Dockerfile commands run as.
- Default user is typically root.
- This Dockerfile:
  - Uses root during setup (installing packages, file permissions)
  - Switches to `nobody` before runtime for safer execution

### COPY

- Copies files from the build **context** into the image.
- Context = the directory passed to `docker build` (often the current directory).
- This copies:
  - entrypoint.bash → /

### RUN

- Executes commands during the image build.
- Often used to install software or configure the filesystem.
- Here it:
  - Updates package lists
  - Installs curl and bash
  - Makes the script executable

### ENTRYPOINT

- Defines the command that containers created from this image will run.
- Containers made from this image will execute:
  - /entrypoint.bash

---

## Building the Docker Image

### Command

```bash
docker build -t our-first-image .
```

### What `-t` Means

- Tags the built image with a friendly name:
  - our-first-image
- Without tagging, you’d rely on image IDs.

### What the Dot (`.`) Means

- The final argument is the **context** path.
- `.` means: current directory (contains Dockerfile + entrypoint.bash).

## What the Build Output Shows

- Docker executes the Dockerfile **line by line**.
- Each Dockerfile instruction produces a build step.
- Docker creates **intermediate layers** (intermediate images) as it goes.
- At the end, Docker outputs a final image and tags it as:
  - our-first-image:latest

---

## Running a Container from the Built Image

### Command

```bash
docker run our-first-image
```

### Output

Hello! The current date and time is Sun, 28 Dec 2025 14:21:35 GMT

### What Happened

- Docker created a container from the `our-first-image` image
- Docker started the container
- The container ran the ENTRYPOINT script:
  - /entrypoint.bash
- The script verified:
  - bash version 5 exists
  - curl is installed
- Then it printed the current time (from the Date header returned by google.com)

---

## Key Takeaways

- A Dockerfile defines how to build an image.
- `docker build` turns a Dockerfile + context into a Docker image.
- `-t` gives the image a human-friendly name.
- `.` selects the current folder as build context.
- `ENTRYPOINT` defines what containers run by default.
- Docker builds images in layers (intermediate steps), then produces the final image.
