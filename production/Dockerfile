FROM ruby:3.3-rc-slim
LABEL maintainer="jiting@jtcat.com"

RUN set -eux; \
  apt-get update; \
  apt-get install -y --no-install-recommends \
    postgresql-client \
    tzdata \
    imagemagick \
  ; \
	rm -rf /var/lib/apt/lists/*

# Configure Rails
ENV RAILS_LOG_TO_STDOUT true
ENV RAILS_SERVE_STATIC_FILES true

WORKDIR /home/app

# Expose Puma port
EXPOSE 3000

# This image is for production env only
ENV RAILS_ENV production

# Set MALLOC_ARENA_MAX to prevent memory fragmentation
ENV MALLOC_ARENA_MAX 2

# Write GIT commit SHA and TIME to env vars
ONBUILD ARG COMMIT_SHA
ONBUILD ARG COMMIT_TIME

ONBUILD ENV COMMIT_SHA ${COMMIT_SHA}
ONBUILD ENV COMMIT_TIME ${COMMIT_TIME}

# Add user
ONBUILD RUN groupadd -g 1000 app && useradd -u 1000 -g app -d /home/app -s /bin/sh app && chown app:app /home/app

# Copy app with gems from former build stage
ONBUILD COPY --from=Builder --chown=app:app /usr/local/bundle/ /usr/local/bundle/
ONBUILD COPY --from=Builder --chown=app:app /home/app /home/app
