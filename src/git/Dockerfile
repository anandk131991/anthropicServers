# Use a Python image with uv pre-installed
FROM ghcr.io/astral-sh/uv:python3.12-bookworm-slim AS uv

WORKDIR /app

ENV UV_COMPILE_BYTECODE=1
ENV UV_LINK_MODE=copy

# COPY entire git server folder (pyproject.toml, uv.lock, src/)
ADD . /app

RUN --mount=type=cache,target=/root/.cache/uv \
    uv sync --frozen --no-dev

# Runtime image
FROM python:3.12-slim-bookworm

RUN apt-get update && apt-get install -y git git-lfs && rm -rf /var/lib/apt/lists/* \
    && git lfs install --system

WORKDIR /app

COPY --from=uv /root/.local /root/.local
COPY --from=uv /app/.venv /app/.venv

ENV PATH="/root/.local/bin:/app/.venv/bin:$PATH"

ENTRYPOINT ["mcp-server-git"]
