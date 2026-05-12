# syntax=docker/dockerfile:1
# -----------------------------------------------------------------------------
# POC container image for a stateful app (e.g. LevelDB workload).
#
# This Dockerfile does NOT use a vendor "hardened" minimal image (e.g. Chainguard,
# distroless). It uses plain **Alpine** with **hardening practices**: non-root user,
# dumb-init as PID 1 helper, small package set, no extra services.
#
# For production, swap `FROM` to one of:
#   - cgr.dev/chainguard/wolfi-base (or static) — minimal, frequent CVE fixes
#   - gcr.io/distroless/*:nonroot — best with a static or copied-binary workload
#   - debian:bookworm-slim + non-root — familiar glibc
#
# Replace the placeholder CMD with your real binary and mount LevelDB PVC at /data.
# -----------------------------------------------------------------------------

FROM alpine:3.20 AS runtime

RUN apk add --no-cache dumb-init ca-certificates tzdata \
    && rm -rf /var/cache/apk/* \
    && addgroup -g 65532 app \
    && adduser -D -H -u 65532 -G app app

WORKDIR /app

# Placeholder process only — swap for COPY --chown=app:app ./your-binary /app/
RUN printf '%s\n' \
      '#!/bin/sh' \
      'echo "replace this image with your application binary"' \
      'exec tail -f /dev/null' \
    > /app/start.sh \
    && chown app:app /app/start.sh \
    && chmod 0555 /app/start.sh

USER app:app

# No root-owned writable paths under / by default; mount LevelDB PVC at /data in K8s.
VOLUME ["/data"]

EXPOSE 8080

ENTRYPOINT ["/usr/bin/dumb-init", "--"]
CMD ["/bin/sh", "/app/start.sh"]
