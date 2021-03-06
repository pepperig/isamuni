FROM ruby:2.4.1

# Adding recent nodejs repositories
RUN curl -sL https://deb.nodesource.com/setup_8.x | bash -

RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        postgresql-client \
        libpq-dev \
        sqlite3 \
        nodejs \
        libsqlite3-dev \
    && rm -rf /var/lib/apt/lists/*

# Install Yarn
RUN curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add - && \
    echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list && \
    apt-get update && \
    apt-get install yarn && \
    rm -rf /var/lib/apt/lists/*

# installing some gems manually
# (either because they shouldn't be in bundler or for caching purposes)

RUN gem install foreman rails

# copying in Gemfile and running bundle install, so that we only do that when
# Gemfile changes

WORKDIR /app
COPY Gemfile* ./
RUN bundle install

COPY package.json ./
RUN yarn install

# copying in the rest of the application
COPY . .
RUN rake assets:precompile

# running the server in production mode by default
EXPOSE 3000
CMD ["bash", "./docker_entrypoint.sh"]
