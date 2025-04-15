FROM ruby:3.4.3-slim
LABEL maintainer="jiting@jtcat.com"

RUN set -eux; \
  apt-get update; \
  apt-get install -y --no-install-recommends \
    postgresql-client \
    tzdata \
    imagemagick \
    libjemalloc2 \
    patchelf \
  ; \
  patchelf --add-needed libjemalloc.so.2 /usr/local/bin/ruby; \
  apt-get purge -y patchelf; \
	rm -rf /var/lib/apt/lists/*

# Configure Rails
ENV RAILS_LOG_TO_STDOUT true
ENV RAILS_SERVE_STATIC_FILES true

WORKDIR /home/app

# Expose Puma port
EXPOSE 3000

# This image is for production env only
ENV RAILS_ENV production

ENV MALLOC_CONF dirty_decay_ms:1000,narenas:2,background_thread:true

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
