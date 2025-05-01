# HomeLab Dockerfiles Repository

This repository is a collection of Dockerfiles crafted to simplify the process of creating and deploying containerized applications for your HomeLab environment. Each branch corresponds to a unique application or service, allowing you to maintain modular and reusable configurations for building Docker images. This structure not only helps ensure consistency and scalability but also makes maintenance efficient and organized for your HomeLab infrastructure.

---

## 🚀 Features

- **Curated Collection**: A variety of Dockerfiles for different applications and services.
- **Version Control**: All configurations are version-controlled, ensuring repeatability and consistency in builds.
- **Branch-Based Organization**: Each branch in the repository corresponds to a specific service or application.
- **GitOps Integration**: Automatically builds and pushes Docker images using [Komodo](https://komo.do/docs/intro).
- **Supports Container Orchestration**: Dockerfiles are optimized for integration with container orchestration tools and CI/CD pipelines.

---

## 📦 Requirements

Before using this repository, ensure you have the following installed on your system:

- [Git](https://git-scm.com/)
- [Docker](https://www.docker.com/) and Docker Compose
- A GitOps tool like [Komodo](https://komo.do/docs/intro) configured with webhook or polling

For Komodo documentation, visit: [Komodo Setup Guide](https://komo.do/docs/intro).

---

## 📁 Repository Structure

The repository's structure is organized in a way that each branch corresponds to a single application or service. Each branch typically contains the following files:

```
.
├── Dockerfile             # Main Docker service definition
├── .env                   # Optional file for environment variables
├── setup.sh               # Optional initialization scripts for setup
├── README.md              # Service-specific documentation (optional)
```

> ✅ Using orphan branches keeps service deployments clean, focused, and version-controlled.

### Example Branches

- `Nginx` – A static web server with a custom configuration.
- `Postgres` – A PostgreSQL container with init scripts.
- `Nodejs-App` – Example configuration for a Node.js service.

---

## ⚙️ Getting Started

### 1. Clone the Repository

Start by cloning the repository to your local system:

```bash
git clone https://{{git-source}}/{{user-name}}/{{repo-name}}.git
cd {{repo-name}}
```

### 2. Create a New Orphan Branch

```bash
git checkout --orphan <ServiceName>
```

### 3. Add Your Files

For each application or service, your branch should contain the following files:

- `Dockerfile` – The main service definition.
- Supporting files – This may include `.env`, scripts, or other configuration files.
- `README.md` – Service-specific notes (optional but recommended for documentation).

---

### 4. Komodo GitOps Automation

After pushing your changes, **Komodo** will automatically:

1. Detect the new commit on your branch.
2. Build the Docker image using the provided `Dockerfile`.
3. Tag the Docker image and push it to the configured container registry.

#### Komodo Setup Instructions

1. **Add Your Repository**: Go to the Komodo UI and add your repository.
2. **Configure Build Pipeline**: Set up the build pipeline with your repository settings, including the repository URL and access credentials.
3. **Enable Auto-Sync**: Configure auto-sync (or manual approval mode) to automatically trigger builds when a commit is detected.
4. **Configure Webhook or Polling**: Set up webhooks or polling in Komodo to trigger builds on new commits.

For detailed instructions on setting up Komodo with your repository, see the [Komodo CI/CD Webhook Setup](https://komo.do/docs/webhooks).

---

### 5. Push the Branch

Once your changes are ready, commit and push the new branch:

```bash
git add .
git commit -m "Add Dockerfile for Nginx"
git push origin Nginx
```

---

## 🧰 Bash Helpers

These aliases and functions can be added to your `.bashrc`, `.zshrc`, or `.bash_aliases` for ease of use.

### Pull All Branches and Sync

This function fetches all remote branches and ensures they are synchronized with your local branches:

```bash
alias pullall='
git fetch --prune --all && \
current_branch=$(git branch --show-current); \
for local_branch in $(git branch --format="%(refname:short)"); do \
  if ! git show-ref --verify --quiet refs/remotes/origin/$local_branch; then \
    echo "🧹 Deleting local branch $local_branch (no longer exists on origin)..."; \
    git branch -D $local_branch; \
  fi; \
done; \
for remote_branch in $(git branch -r | grep -v "\->" | sed "s|origin/||"); do \
  if git show-ref --verify --quiet refs/heads/$remote_branch; then \
    echo "🔍 Checking branch $remote_branch (already exists locally)..."; \
    ahead=$(git rev-list --left-right --count $remote_branch...origin/$remote_branch | awk "{print \$1}"); \
    behind=$(git rev-list --left-right --count $remote_branch...origin/$remote_branch | awk "{print \$2}"); \
    if [ "$ahead" = "0" ] && [ "$behind" != "0" ]; then \
      echo "  ⏩ Fast-forwarding $remote_branch..."; \
      git update-ref refs/heads/$remote_branch refs/remotes/origin/$remote_branch; \
    elif [ "$ahead" = "0" ] && [ "$behind" = "0" ]; then \
      echo "  ✅ $remote_branch is already up-to-date."; \
    else \
      echo "  ⚠️ $remote_branch has diverged! Needs manual pull/merge."; \
    fi; \
  else \
    echo "🌱 Creating missing local branch $remote_branch tracking origin/$remote_branch..."; \
    git branch $remote_branch origin/$remote_branch; \
  fi; \
done; \
git checkout $current_branch'
```

### Orphan Branch Creator

This function creates an orphan branch, which is useful for starting a new service or application:

```bash
orphan() {
    if [ -z "$1" ]; then
        echo "Usage: orphan <branch-name>"
        return 1
    fi
    git checkout --orphan "$1"
}
```

### Enable Git Autocompletion

To enable Git autocompletion in your terminal, add this line:

```bash
source /usr/share/bash-completion/completions/git
```

---

## 📝 Best Practices

- **Keep Dockerfiles Clean**: Avoid unnecessary packages and complexity. Keep them as simple and modular as possible.
- **Use Official Base Images**: Whenever possible, use official or well-maintained base images for better security and stability.
- **Store Secrets Securely**: Do not hardcode secrets into Dockerfiles. Use environment variables or external secrets management tools.
- **Document Everything**: Include a `README.md` in every branch to explain the purpose of the service and any important configurations.

---

## 🔁 CI/CD Flow with Komodo

The CI/CD flow involves multiple steps that automate the build and deployment process:

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

---

## 🔧 Deployment Troubleshooting

### 1. Build Failures

If your Docker build fails, common issues include:

- **Missing dependencies**: Ensure all required files are in the Docker context.
- **Dockerfile syntax errors**: Double-check for typos or incorrect commands.

### 2. Komodo Build Failures

If Komodo's build pipeline fails, check:

- **Build Logs**: Review the logs in the Komodo UI for detailed error messages.
- **Missing Files**: Ensure all necessary files are committed and available for the build.
- **Branch Configuration**: Verify that the correct branch is being watched by Komodo.

### 3. Deployment Not Triggering

If your deployment doesn't trigger:

- **Webhook Issues**: Ensure the webhook or polling is set up correctly.
- **Sync Problems**: Manually trigger a build in Komodo or check sync settings.

### 4. Docker Deployment Issues

If your container isn't working correctly:

- **Port Conflicts**: Check that the ports are available and not already in use.
- **Environment Variables**: Verify that the correct environment variables are set.
- **Logs**: Use `docker logs <your-container-name>` to review container logs for errors.

---
**THIS REPOSITORY IS ENCRYPTED. IF YOU'RE HERE, YOU'RE EITHER VERY BRAVE OR VERY LOST. EITHER WAY, GOOD LUCK!**
