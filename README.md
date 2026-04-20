# images

A repository for building and publishing OCI container images to the [GitHub Container Registry](https://ghcr.io) using Podman/Buildah.

## Repository layout

Each container lives in its own top-level directory containing a single `Containerfile`:

```
images/
├── my-app/
│   └── Containerfile
└── another-app/
    └── Containerfile
```

## Adding a new container

1. Create a directory named after your image:
   ```bash
   mkdir my-app
   ```
2. Add a `Containerfile` inside it:
   ```bash
   cat > my-app/Containerfile <<'EOF'
   FROM alpine:3.21
   RUN apk add --no-cache curl
   CMD ["sh"]
   EOF
   ```
3. Commit and open a PR — CI will build the image and verify the commit message.

On merge to `main` the image is pushed to:
```
ghcr.io/thewizardofozolins/<directory-name>:latest
ghcr.io/thewizardofozolins/<directory-name>:<short-sha>
```

## CI workflows

| Workflow | Trigger | What it does |
|---|---|---|
| **Build Container Images** | push / PR to `main` | Detects changed `Containerfile`s, builds with Buildah, pushes to GHCR on merge |
| **Lint Commits** | push / PR to `main` | Validates every commit message against the Conventional Commits spec |

## Commit messages

All commits must follow [Conventional Commits 1.0.0](https://www.conventionalcommits.org/en/v1.0.0/).

```
<type>[optional scope]: <description>

feat(my-app): add curl to base image
fix: correct entrypoint path
chore: update alpine base to 3.21
```

Valid types: `feat` `fix` `docs` `style` `refactor` `perf` `test` `build` `ci` `chore` `revert`

### Local hook setup

After cloning, activate the commit-msg hook once:

```bash
git config core.hooksPath .githooks
```

The hook rejects non-conforming messages before they are recorded locally, mirroring what CI checks on every PR.

## Pulling an image

```bash
podman pull ghcr.io/thewizardofozolins/my-app:latest
```
