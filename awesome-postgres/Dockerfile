FROM postgres:9.5
MAINTAINER Eric Fulmer <eric.r.fulmer@gmail.com>

RUN apt-get update && apt-get install -y --no-install-recommends ca-certificates \
    wget \
    python-pip \
    curl \
    build-essential \
    libssl-dev \
    libkrb5-dev \
    postgresql-server-dev-${PG_MAJOR} \
    libpq-dev

# Install PL/Python
RUN apt-get install -y postgresql-plpython3-9.5

# Install temporal tables extension
RUN pip install pgxnclient && pgxn install temporal_tables

RUN apt-get install -y postgresql-${PG_MAJOR}-pgq3

# Cleanup
RUN apt-get update -y -qq --fix-missing \
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/*

RUN mkdir -p /tmp/pgaudit && cd /tmp/pgaudit && curl -L https://github.com/pgaudit/pgaudit/archive/1.0.4.tar.gz | tar xz --strip 1
RUN cd /tmp/pgaudit && USE_PGXS=1 make -s check && USE_PGXS=1 make install

# Configuration
COPY config/postgresql.conf "/usr/share/postgresql/${PG_MAJOR}/postgresql.conf.sample"
COPY config/pg_hba.conf "/usr/share/postgresql/${PG_MAJOR}/pg_hba.conf"
COPY docker-entrypoint.sh /docker-entrypoint.sh
COPY init.sh /docker-entrypoint-initdb.d/init.sh
