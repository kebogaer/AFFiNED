# syntax=docker/dockerfile:1.7
# Official AFFiNE 0.27.4 image with the patched server native module
# (BYOK key test fix). Everything else is unchanged.
ARG BASE_IMAGE=ghcr.io/toeverything/affine:0.27.4
FROM ${BASE_IMAGE}

ARG TARGETARCH
ARG TARGETVARIANT
COPY native/ /tmp/byok-fix-native/

RUN set -eu; \
  case "${TARGETARCH}${TARGETVARIANT:+/$TARGETVARIANT}" in \
    amd64) own=x64 ;; \
    arm64) own=arm64 ;; \
    arm/v7) own=armv7 ;; \
    *) echo "unsupported platform ${TARGETARCH}" >&2; exit 1 ;; \
  esac; \
  replaced=0; \
  for f in $(find /app -name 'server-native*.node' -type f); do \
    case "$(basename "$f")" in \
      server-native.x64.node) src=x64 ;; \
      server-native.arm64.node) src=arm64 ;; \
      server-native.armv7.node) src=armv7 ;; \
      *) src="$own" ;; \
    esac; \
    if [ -f "/tmp/byok-fix-native/server-native.$src.node" ]; then \
      cp "/tmp/byok-fix-native/server-native.$src.node" "$f"; \
      echo "patched $f"; \
      replaced=$((replaced + 1)); \
    fi; \
  done; \
  rm -rf /tmp/byok-fix-native; \
  test "$replaced" -gt 0 || { echo "no server-native module found to patch" >&2; exit 1; }
