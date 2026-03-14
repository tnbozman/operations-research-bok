# operations-research-bok

A quick-start guide to Operations Research for engineers, with a focus on military operations applications.

## Development

### Using the Dev Container (Recommended)

This repository includes a [dev container](https://containers.dev/) configuration for a consistent development environment with [MkDocs](https://www.mkdocs.org/) and [uv](https://docs.astral.sh/uv/).

1. Open the repository in [VS Code](https://code.visualstudio.com/) with the [Dev Containers](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers) extension installed.
2. Click **Reopen in Container** when prompted.
3. Once the container is ready, serve the docs locally:

```bash
uv run mkdocs serve
```

Open your browser at <http://localhost:8000> to preview the site.

### Local Setup with uv

1. Install [uv](https://docs.astral.sh/uv/getting-started/installation/):

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

2. Install dependencies:

```bash
uv sync
```

3. Serve the docs:

```bash
uv run mkdocs serve
```

### Build

```bash
uv run mkdocs build
```