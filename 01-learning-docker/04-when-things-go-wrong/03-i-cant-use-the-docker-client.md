# 03 – I Can’t Use the Docker Client

Sometimes you open a terminal, run a simple Docker command like `docker ps`, and suddenly Docker responds with something like:

> Is the Docker daemon running?

This can be frustrating—especially when everything worked fine yesterday.  

---

## Common Symptom

- Running `docker ps` or any Docker command fails
- Docker complains that it cannot connect to the Docker daemon

---

## Cause 1: Docker Desktop Is Not Running (Most Common)

On macOS and Windows, Docker runs through **Docker Desktop**.

### What to Check

- Look for the Docker whale icon in:
  - System tray (Windows)
  - Menu bar (macOS)

### Fix

- If Docker Desktop is not running:
  - Open Docker Desktop
  - Wait until it fully starts
  - Look for the whale icon with boxes on top

Once Docker Desktop is running, retry:

docker ps

In most cases, this immediately fixes the issue.

---

## Cause 2: Wrong or Broken Docker Context

A less common—but very real—cause is using the wrong **Docker context**.

Docker contexts define:

- Which Docker engine the client talks to
- Whether it’s local, remote, or virtualized

---

## Listing Docker Contexts

### Command

docker context ls

### What You’ll See

- A list of available contexts
- An asterisk (`*`) next to the currently active context
- Endpoint information for each context

If the active context points to:

- A non-existent socket
- A remote machine that’s offline

Docker commands will fail.

---

## Switching Back to a Working Context

### Command

docker context use default

or (on Docker Desktop systems):

docker context use desktop-linux

After switching, retry:

docker ps

If the context was the issue, Docker should now work normally.

---

## Environment Variable Overrides (Advanced Case)

Docker contexts and endpoints can be overridden by environment variables.

Important ones:

- `DOCKER_CONTEXT`
- `DOCKER_HOST`

---

## Checking for Overrides

### Command (macOS / Linux)

echo $DOCKER_CONTEXT
echo $DOCKER_HOST

### Command (Windows PowerShell)

echo $env:DOCKER_CONTEXT
echo $env:DOCKER_HOST

If these return values, Docker may be ignoring your selected context.

---

## Example: Breaking Docker with an Override

Setting an invalid context:

export DOCKER_CONTEXT=definitely-not-working

Now running:

docker ps

…will fail because Docker is forced to use a non-existent context.

---

## Fix: Remove the Override

### Command

unset DOCKER_CONTEXT

After unsetting it, Docker will return to normal behavior.

---

## Key Takeaways

- Most Docker client issues happen because Docker Desktop isn’t running
- Docker contexts control which engine the client talks to
- A broken or outdated context can completely block Docker commands
- Environment variables can override contexts and endpoints
- Resetting to the default context usually fixes the issue quickly

This is a very common problem—and now you know exactly how to fix it.
