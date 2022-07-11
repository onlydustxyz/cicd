####################################################################################################
## Builder
####################################################################################################
FROM rust:1.61 AS builder

RUN update-ca-certificates

# Create unprivileged app user
RUN adduser --disabled-password --gecos "" od-app-user

WORKDIR /app

COPY ./ .

RUN cargo build --release

####################################################################################################
## Final image
####################################################################################################
FROM debian:stable

ARG BIN_NAME
ARG DATADOG_LABEL

# Import from builder.
COPY --from=builder /etc/passwd /etc/passwd
COPY --from=builder /etc/group /etc/group

WORKDIR /app

# Copy our build
COPY --from=builder /app/target/release/ /app/

# "Rename" executable file for CMD
COPY --from=builder "/app/target/release/${BIN_NAME}" /app/executable

# Install dependencies and CA certificates
RUN apt-get update && apt-get install -y postgresql-client-13 ca-certificates
RUN update-ca-certificates

# Add labels
LABEL "com.datadoghq.ad.logs"="${DATADOG_LABEL}"

# Use app user
USER od-app-user:od-app-user

CMD ["/app/executable"]
