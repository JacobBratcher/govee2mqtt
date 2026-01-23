ARG BUILD_FROM

# Build stage - compile from source
FROM rust:1.75-bookworm AS builder

RUN apt-get update && apt-get install -y \
    pkg-config \
    libssl-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /build

# Copy source files (build context must be repo root)
COPY Cargo.toml Cargo.lock build.rs ./
COPY src ./src
COPY assets ./assets
COPY AmazonRootCA1.pem ./

RUN cargo build --release

# Runtime stage
FROM $BUILD_FROM

# Copy the built binary and assets
COPY --from=builder /build/target/release/govee /app/govee
COPY --from=builder /build/assets /app/assets/
COPY --from=builder /build/AmazonRootCA1.pem /app/

COPY addon/run.sh /run.sh
RUN chmod +x /run.sh

CMD [ "/run.sh" ]