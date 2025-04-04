# HomeLab Dockerfiles Repository

This repository contains a collection of Dockerfiles designed to streamline the creation and deployment of containerized applications in a HomeLab environment. Each branch in this repository represents a specific application or service, providing a modular and reusable configuration for building Docker images. This structure ensures consistency, scalability, and ease of maintenance for your HomeLab infrastructure.

## Features

- A curated collection of Dockerfiles for various applications and services.
- Modular and reusable configurations for easy customization.
- Version-controlled templates for consistent builds.
- Organized into branches, each representing a specific application or service.
- **Integrated with [Komodo](https://komo.do/docs/intro)** for automated builds and image pushes.
- Designed to work with container orchestration tools and GitOps CI/CD pipelines.

---

## Requirements

- Git installed
- Docker installed
- A GitOps tool like [Komodo](https://komo.do/docs/intro) for build automation

---

## Repository Structure

Each branch corresponds to a single app or service, with:

- A `Dockerfile`
- Supporting files (e.g., configs, scripts, assets)
- An optional `README.md` with details and usage notes

### Example Branches

- `Nginx` – Static web server with custom config
- `Postgres` – PostgreSQL with init scripts
- `Nodejs-App` – Example Node.js service

---

## Getting Started

### 1. Clone the Repository

```bash
git clone https://{{git-source}}/homelab10400/homelab-dockerfiles.git
cd homelab-dockerfiles
```

---

### 2. Create a New Branch

Follow the naming convention:

```yaml
Application-Name
```

Then:

```bash
git checkout --orphan Application-Name
```

---

### 3. Add Your Files

Each branch should include:

- `Dockerfile`
- Supporting files (`.env`, scripts, etc.)
- `README.md` (optional, but recommended)

---

### 4. Komodo GitOps Automation

Once you push your changes, **Komodo automatically:**

1. Detects the new commit
2. Builds the Docker image using your `Dockerfile`
3. Tags and pushes the image to your configured container registry

#### Komodo Setup Instructions:

- Add your repository in the Komodo UI
- Configure the build pipeline
- Enable auto-sync (or manual approve mode)
- Set a webhook or polling for commit detection

See full docs: [Komodo CI/CD Webhook Setup](https://komo.do/docs/webhooks)

---

### 5. Push the Branch

```bash
git add .
git commit -m "Add Dockerfile for Nginx"
git push origin Nginx
```

---

## Bash Helpers

You can include these in your `.bashrc` or `.bash_aliases`:

```bash
# Pull all branches
alias pullall="for branch in \$(git branch | sed 's/^\* //'); do git checkout \$branch && git pull; done"

# Git autocomplete
source /usr/share/bash-completion/completions/git

# Create an orphan branch easily
orphan() {
  if [ -z "$1" ]; then
    echo "Usage: orphan <branch-name>"
    return 1
  fi
  git checkout --orphan "$1"
}
```

---

## Best Practices

- Keep Dockerfiles clean and minimal
- Use official base images where possible
- Store secrets in external systems or use environment variables
- Document your setup in the `README.md` per branch

---

## CI/CD Flow with Komodo

```
Git Branch (e.g., Nginx)
        ↓
  Git Commit & Push
        ↓
     Komodo CI/CD
        ↓
 Docker Build & Tag
        ↓
 Docker Image Pushed to Registry
        ↓
     Optional Deployment Trigger
```

**THIS REPOSITORY IS ENCRYPTED. IF YOU'RE HERE, YOU'RE EITHER VERY BRAVE OR VERY LOST. EITHER WAY, GOOD LUCK!**
