FROM debian:stable-slim AS builder

RUN apt-get update && apt-get install -y --no-install-recommends \
    curl \
    ca-certificates \
    git \
    build-essential \
    pkg-config \
    libssl-dev \
    && rm -rf /var/lib/apt/lists/*

RUN curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y --default-toolchain stable --profile minimal
ENV PATH="/root/.cargo/bin:${PATH}"

ARG UV_VERSION=latest
RUN if [ "$UV_VERSION" = "latest" ]; then \
        git clone --depth 1 https://github.com/astral-sh/uv.git /src/uv; \
    else \
        git clone --depth 1 --branch "$UV_VERSION" https://github.com/astral-sh/uv.git /src/uv; \
    fi

WORKDIR /src/uv
RUN cargo build --release --locked -p uv


FROM debian:stable-slim

RUN apt-get update && apt-get install -y --no-install-recommends \
    ca-certificates \
    && rm -rf /var/lib/apt/lists/*

COPY --from=builder /src/uv/target/release/uv /usr/local/bin/uv

RUN uv --version

CMD ["uv"]
