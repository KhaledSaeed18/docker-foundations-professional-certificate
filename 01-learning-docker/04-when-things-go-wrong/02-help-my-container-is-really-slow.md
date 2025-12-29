# 02 – Help! My Container Is Really Slow

Sometimes a container runs much more slowly than expected.
There are many advanced ways to debug performance issues, but three Docker commands give you **quick, practical insight** right away:

- `docker stats`
- `docker top`
- `docker inspect`

---

## Test Setup: A Long-Running Container

To demonstrate performance debugging, we use a lightweight container.

### Why Alpine?

- Very small Linux distribution
- Starts quickly
- Ideal for testing and debugging containers

The container is configured to:

- Run in the background
- Sleep forever (so it stays alive)

---

## Command: docker stats

### Purpose

Shows **real-time resource usage** of running containers.

It answers questions like:

- Is CPU usage high?
- Is memory usage growing?
- Is the container idle or overloaded?

### Usage

docker stats <container>

You can use either:

- Container ID
- Container name

### What You See

- CPU %
- Memory usage / limits
- Network I/O
- Block I/O
- Process count

This is usually the **first command** to run when a container feels slow.

---

## Forcing High CPU Usage (Test Scenario)

Inside the container, a shell is started using `docker exec`.
Then a simple program is run to consume CPU:

- The `yes` command prints endlessly
- This quickly drives CPU usage very high

While this runs:

- `docker stats` shows CPU usage spike
- After stopping it, CPU usage drops back down

This confirms:

- The slowdown is CPU-related
- The container responds immediately when load changes

---

## Command: docker top

### Purpose

Shows **what processes are running inside the container**  
(without entering it).

This is useful when:

- A container is slow
- You don’t know what’s running inside
- You want a process-level view

### Usage

docker top <container>

### What You See

- Process IDs
- Commands being executed
- Multiple background processes (if any)

Example insight:

- Seeing multiple `sleep` processes
- Detecting runaway or unexpected processes

This helps answer:
> “What exactly is running in my container right now?”

---

## Command: docker inspect

### Purpose

Provides **detailed, low-level container information** in JSON format.

This is useful for:

- Deep debugging
- Automation and scripting
- Verifying configuration

### Usage

docker inspect <container>

Because the output is long, it’s often viewed page-by-page:

docker inspect <container> | less

---

## What docker inspect Can Show

Examples of valuable information:

- Restart count
- Restart policy
- Mounts / volumes
- Networking details
- Resource limits
- Container state
- Creation timestamps

This data goes far beyond what `docker ps` shows.

---

## When to Use Each Command

### docker stats

- First stop for performance issues
- Real-time CPU and memory usage

### docker top

- Process-level debugging
- See what’s actually running inside

### docker inspect

- Configuration and state debugging
- Best for deep dives and automation

---

## Key Takeaways

- Slow containers are usually resource-related
- `docker stats` shows live performance
- `docker top` reveals internal processes
- `docker inspect` exposes full container configuration
- These three commands form a strong foundation for Docker troubleshooting

With these tools, you can quickly narrow down where problems come from before diving deeper.
