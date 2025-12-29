# 04 – Solution: Fix a Broken Container

This challenge walks through a realistic debugging workflow:

1) A build fails because of a bad base image tag  
2) The container runs *slow* due to a CPU-hogging command  
3) You fix the script, rebuild the image, and rerun cleanly

---

## Exercise Files

04_03_before % ls  
Dockerfile      app.sh

---

## Part A – Fix the Broken Build (Base Image Tag)

### What We Tried

docker build -t app .

### Output (Build Failure)

```bash
[+] Building 0.7s (2/2) FINISHED                                                                                              docker:desktop-linux  
 => [internal] load build definition from Dockerfile                                                                                          0.0s  
 => => transferring dockerfile: 192B                                                                                                          0.0s  
 => ERROR [internal] load metadata for docker.io/library/ubuntu:xeniall                                                                       0.6s  
------  
 >
 > [internal] load metadata for docker.io/library/ubuntu:xeniall:  
------  

Dockerfile:2  
--------------------  

1 |     # HINT: Remember that you can find `ubuntu` images in the Docker Hub!  
   2 | >>> FROM ubuntu:xeniall  
   3 |
   4 |     COPY /app.sh /  
--------------------  

ERROR: failed to build: failed to solve: ubuntu:xeniall: failed to resolve source metadata for docker.io/library/ubuntu:xeniall: docker.io/library/ubuntu:xeniall: not found
```

### Root Cause

The base image tag is misspelled:

- Dockerfile used: `ubuntu:xeniall`
- Correct tag: `ubuntu:xenial`

The error is Docker telling you:
> I cannot find `ubuntu:xeniall` locally or in Docker Hub.

---

## Fix: Correct the FROM Tag

### Original Dockerfile (Broken)

FROM ubuntu:xeniall

### Updated Dockerfile (Fixed)

FROM ubuntu:xenial

After fixing, rerun:

docker build -t app .

Now the build succeeds.

---

## Part B – Fix the Slow Container (High CPU)

### Run the Container

docker run -it app

The app runs, but **very slowly**.
The prompt says it should be immediate, so something is wrong.

---

## Diagnose: Check Container Resource Usage

### 1) Use docker stats

Shows live CPU/memory usage.

docker stats <container>

When rerunning the app, the CPU is heavily used → strong sign of a CPU-hogging process.

### 2) Use docker top

Shows which processes are running inside the container.

docker top <container>

This reveals unexpected processes, including `yes`.

Important: we learned earlier that `yes` can “light the CPU on fire”.

---

## Root Cause in app.sh (Before)

### Original app.sh (Problem)

# !/usr/bin/env bash

start() {
  echo "Application starting"
}

process() {
  _process() {
    timeout "$((i*i))" yes &>/dev/null
    echo "Application processing ($1/5)"
  }

  for i in $(seq 1 5)
  do _process "$i"
  done
}

finish() {
  echo "Application finished!"
}

start &&
  process &&
  finish

### Why It’s Slow

This line is the culprit:

timeout "$((i*i))" yes &>/dev/null

- `yes` consumes CPU aggressively
- `timeout` lets it run for increasing durations
- Result: high CPU usage, slow processing, unnecessary work

---

## Fix app.sh (Remove the CPU Hog)

### Updated app.sh (Fixed)

# !/usr/bin/env bash

start() {
  echo "Application starting"
}

process() {
  _process() {
    echo "Application processing ($1/5)"
  }

  for i in $(seq 1 5)
  do _process "$i"
  done
}

finish() {
  echo "Application finished!"
}

start &&
  process &&
  finish

Now the processing prints immediately and completes quickly.

---

## Rebuild the Image After Code Changes

Whenever you change files that are copied into the image, rebuild:

docker build -t app .

---

## Common Issue After Rebuild: Name Conflict

If you rerun with a container name that already exists, Docker may complain the name is already in use.

Fix by removing the old container:

docker rm <container-name>

Then run again.

---

## Final State (After Fix)

- Dockerfile builds correctly (`ubuntu:xenial`)
- Container runs fast (no `yes` CPU bomb)
- Output appears immediately and finishes quickly

---

## Key Takeaways

- Build failures often come from bad image tags (`FROM ...`)
- Docker’s error message usually points directly to the problem line
- For “slow containers”, start with:
  - `docker stats` (resource usage)
  - `docker top` (processes inside)
- If code changes are inside the image, you must rebuild (`docker build ...`)
- Container name conflicts are solved by removing old containers (`docker rm`)
