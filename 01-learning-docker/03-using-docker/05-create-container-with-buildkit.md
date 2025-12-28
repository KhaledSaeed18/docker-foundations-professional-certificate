# 05 – Create a Docker Container with BuildKit

This section introduces **BuildKit**, Docker’s modern image builder, and shows how to build and run images using **docker buildx**.

---

## Why BuildKit?

Docker historically used a legacy image builder.  
If build output looks older or lacks caching indicators, the legacy builder may be in use.

**BuildKit** is Docker’s improved builder and provides:

- Faster builds
- Better caching
- Advanced features (exporters, multi-platform builds)
- More control over build outputs

Modern versions of Docker Desktop use BuildKit internally, but **Buildx** exposes its full capabilities.

---

## Docker Buildx

Buildx is the CLI interface to BuildKit.

### Command

```bash
docker buildx
```

### Output

Usage:  docker buildx [OPTIONS] COMMAND

Extended build capabilities with BuildKit

Options:
--builder string   Override the configured builder instance  
-D, --debug        Enable debug logging  

Commands:

- bake        Build from a file
- build       Start a build
- create      Create a new builder instance
- inspect     Inspect current builder
- ls          List builders
- prune       Remove build cache
- version     Show Buildx version

### Key Idea

Buildx allows:

- Multiple builder instances
- Advanced build workflows
- Cross-platform builds (Intel / ARM)
- Remote or Kubernetes-based builds

---

## Building an Image with BuildKit

### Command

```bash
docker buildx build -t our-first-image --load .
```

### Explanation

- `buildx build` uses BuildKit
- `-t our-first-image` tags the image
- `--load` tells BuildKit to load the image into the local Docker image store
- `.` sets the build context (current directory)

### Why `--load` Is Important

BuildKit supports **exporters**, which define where the built image goes:

- Push directly to a registry
- Export as a tar file
- Load into local Docker

`--load` is a shortcut that:

- Builds the image
- Loads it into Docker automatically

---

### What The output Shows

- BuildKit uses **layer caching** aggressively
- Previously built steps are marked as **CACHED**
- Builds complete much faster than the legacy builder
- Final image is tagged as `our-first-image:latest`

---

## Builders (Behind the Scenes)

When Buildx is run for the first time:

- Docker creates **builder containers**
- These containers perform image builds

Builders enable:

- Multi-architecture builds (ARM, x86)
- Remote builds
- Kubernetes-based builds

---

## Running the BuildKit Image

### Command

```bash
docker run --rm our-first-image
```

### Output

Hello! The current date and time is Sun, 28 Dec 2025 14:50:36 GMT

### Explanation

- The image behaves exactly like one built with `docker build`
- `--rm` removes the container after it exits
- ENTRYPOINT runs the same script as before

---

## Key Takeaways

- BuildKit is Docker’s modern image builder
- `docker buildx` exposes advanced BuildKit features
- `--load` imports the built image into local Docker
- BuildKit improves speed through caching
- Images built with BuildKit run the same as legacy-built images
