# 13 – Beyond Docker Hub: Other Popular Container Registries

Docker Hub is the most common and beginner-friendly container registry, but it is **not the only option**.
In real-world projects, you will often work with other registries depending on your workflow, platform, or organization.

This section introduces the most common alternatives and when they are used.

---

## GitHub Container Registry (GHCR)

GitHub provides a built-in container registry that integrates directly with GitHub repositories.

### Why It’s Popular

- Free for public projects
- Tight integration with GitHub Actions
- Images can live alongside source code
- Simple authentication using GitHub tokens

### Common Use Case

- Open-source projects
- CI/CD pipelines using GitHub Actions
- Teams already hosting code on GitHub

Images are typically named like:
ghcr.io/username/image-name:tag

---

## GitLab Container Registry

GitLab also provides a built-in container registry tightly integrated with GitLab CI/CD.

### Key Advantages

- Free private container registries
- Built-in CI/CD with GitLab CI
- Strong access control
- Good choice for teams wanting private images without extra cost

### Common Use Case

- Private projects
- Teams using GitLab for source control and CI
- Organizations needing more control over image visibility

---

## Private / Enterprise Container Registries

In corporate or large-scale environments, teams often use **self-hosted or enterprise registries**.

### Benefits of Private Registries

- Role-based access control (RBAC)
- Service accounts for automation
- Image scanning for security vulnerabilities
- Better compliance and auditing
- Full control over image lifecycle

### Popular Options

- JFrog Artifactory
- Sonatype Nexus
- Red Hat Quay
- Harbor (open source)

---

## Cloud Provider Registries

All major cloud providers offer managed container registries.

Examples include:

- AWS Elastic Container Registry (ECR)
- Google Artifact Registry / Container Registry
- Azure Container Registry (ACR)

### Common Use Case

- Cloud-native deployments
- Kubernetes clusters hosted by cloud providers
- Reduced latency and tighter cloud integration

---

## Same Docker Workflow Everywhere

No matter which registry you use, the **Docker commands stay the same**:

- docker login
- docker build
- docker tag
- docker push
- docker pull

Only the **image name and registry prefix** change.

This means:

- Skills learned with Docker Hub transfer everywhere
- Switching registries is mostly a configuration change

---

## Key Takeaways

- Docker Hub is just one of many container registries
- GitHub and GitLab registries are popular for code-first workflows
- Private registries are common in enterprise environments
- Cloud providers offer native registries for cloud deployments
- The Docker CLI workflow remains the same across all registries
