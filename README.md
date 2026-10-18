# app-template

Template for a new application deployed by the shared CI/CD
([`lrozewicz/.github`](https://github.com/lrozewicz/.github)) to Kubernetes via Argo CD.

## Flow

```
feature branch --PR--> main --tag v1.2.3--> release
   tests, build,       build, push,          same image promoted,
   Trivy               deploy to dev         deploy to prod
```

Release: `git tag v1.2.3 && git push origin v1.2.3` (or a GitHub Release) on a commit of `main`
that is already running on dev.

- **dev:** `https://<app>.dev.lucasnextgen.com` (basic auth)
- **prod:** `https://<app>.lucasnextgen.com`

## Starting a new app

1. Create the repository from this template (**Use this template**).
2. The maintainer runs the bootstrap script for the new repo (Harbor project, secrets, branch rules, GitOps entry).
3. Replace the example Go code and the `Dockerfile` stages with your stack, keeping the contract below.
4. Open a PR to `main`.

## Contract

- `Dockerfile` with stages `test` (tests and linters) and `runtime`.
- The app listens on port `8080` and answers `GET /healthz` with 200.
- Runs as UID `10001` with a read-only root filesystem; only `/tmp` is writable.
- Configuration through environment variables.

Run the CI checks locally:

```bash
docker build --target test .
docker build --target runtime -t app . && docker run --rm -p 8080:8080 --read-only --user 10001 app
```
