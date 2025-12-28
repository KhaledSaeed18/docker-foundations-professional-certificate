# Docker Alternatives

Docker is popular, but it is not the only container solution.

## Container Runtime Interface (CRI)

Kubernetes introduced **CRI** to standardize container runtimes.

This allows:

- Multiple runtimes
- Pluggable container engines
- Custom security and performance tradeoffs

## Examples of CRI-Compatible Runtimes

- **runc**
- **CRI-O**
- **Firecracker (AWS)**

These focus on:

- Performance
- Security
- Cloud-scale workloads

## Security Concerns with Docker

By default:

- Docker containers often run as **root**
- Containers can potentially access the host system

This increases security risks if misconfigured.

## Podman

Podman was created to address Docker security concerns.

### Podman Features

- Rootless containers
- Better security by default
- Docker-compatible CLI
- Supports system services (e.g., systemd)

Podman is commonly used for:

- Secure environments
- Enterprise workloads

## Key Takeaway

Docker is:

- Developer-friendly
- Fast to adopt
- Excellent for learning and productivity

Other tools may be preferred when:

- Maximum security is required
- Large-scale orchestration is involved
