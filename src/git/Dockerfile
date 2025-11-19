# Use a Python image with uv pre-installed
FROM ghcr.io/astral-sh/uv:python3.12-bookworm-slim AS uv

WORKDIR /app

ENV UV_COMPILE_BYTECODE=1
ENV UV_LINK_MODE=copy

# Copy project files first (uv needs full source for project install)
ADD . /app

# Install dependencies AND the project
RUN --mount=type=cache,target=/root/.cache/uv \
    uv sync --frozen --no-dev


# --- Runtime Image ---
FROM python:3.12-slim-bookworm

RUN apt-get update && apt-get install -y git git-lfs && rm -rf /var/lib/apt/lists/* \
    && git lfs install --system

WORKDIR /app

COPY --from=uv /root/.local /root/.local
COPY --from=uv /app/.venv /app/.venv

# Ensure executable is visible
ENV PATH="/root/.local/bin:/app/.venv/bin:$PATH"

ENTRYPOINT ["mcp-server-git"]
