# --- Build Stage ---
FROM ghcr.io/astral-sh/uv:python3.12-bookworm-slim AS uv

WORKDIR /app

ENV UV_COMPILE_BYTECODE=1
ENV UV_LINK_MODE=copy

# Copy ONLY the git server folder
# (Render build context is src/git)
ADD . /app

# Install dependencies AND the project
RUN --mount=type=cache,target=/root/.cache/uv \
    uv sync --frozen --no-dev

# --- Runtime Stage ---
FROM python:3.12-slim-bookworm

RUN apt-get update && apt-get install -y git git-lfs && rm -rf /var/lib/apt/lists/* \
    && git lfs install --system

WORKDIR /app

COPY --from=uv /root/.local /root/.local
COPY --from=uv /app/.venv /app/.venv
COPY --from=uv /app /app

ENV PATH="/root/.local/bin:/app/.venv/bin:$PATH"

ENTRYPOINT ["mcp-server-git"]
#ENTRYPOINT ["bash", "-c", "ls -R /app/.venv/bin && sleep 3600"]
