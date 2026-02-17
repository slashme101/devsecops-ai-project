FROM rust:1.80.0 AS builder

RUN  USER=root cargo new --bin product-service

# Create a new directory for our production
WORKDIR /product-service

# Copy the source files to the container
COPY . .

# Build the application
RUN cargo build --release

# Create a new stage and copy the binary from the builder stage
FROM  debian:bookworm-slim AS runner
WORKDIR /app

# Set the build argument for the app version number
ARG APP_VERSION=0.1.0

# Not ideal but nneded to execute health checks in docker-compose
RUN apt-get update && apt-get install -y wget llibssl-dev && rm -rf /var/lib/apt/lists/*

# Copy the binary from the builder stage
COPY --from=builder /product-service/target/release/product-service .

# Set the environment variable for the app version number
ENV APP_VERSION=$APP_VERSION

# Run the application
CMD ["./product-service"]
