FROM postgres:9.5
MAINTAINER Adam Stankiewicz <sheerun@sher.pl>

ENV ZOMBODB_VER 3.0.0
ENV POSTGIS_VER 2.2
ENV SKYTOOLS_VERSION 3.2.6-4.pgdg80+1

RUN apt-get update && apt-get install -y --no-install-recommends ca-certificates wget python-pip curl build-essential libssl-dev libkrb5-dev


RUN apt-get install -y --no-install-recommends postgresql-server-dev-${PG_MAJOR} libpq-dev

# this is the line i added
RUN apt-get install -y postgresql-plpython3-9.5

# Install zombo
RUN cd / && mkdir zombodb && cd zombodb \
  && wget https://github.com/zombodb/zombodb/releases/download/v${ZOMBODB_VER}/zombodb_trusty_pg95-${ZOMBODB_VER}_amd64.deb \
  && cd /zombodb && dpkg -i zombodb_trusty_pg95-${ZOMBODB_VER}_amd64.deb

# Install temporal tables extension
RUN pip install pgxnclient && pgxn install temporal_tables

RUN apt-get install -y --no-install-recommends postgresql-${PG_MAJOR}-postgis-${POSTGIS_VER}

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
