ARG ehrbase_version=0.9.0
ARG jsquery_version=ver_1.1.1
ARG postgres_version=10
ARG temporal_tables_version=v1.2.0

FROM postgres:$postgres_version-alpine

ARG ehrbase_version
ARG jsquery_version
ARG temporal_tables_version

RUN apk add --no-cache --virtual build-deps bison flex g++ git make

RUN mkdir /tempdb \
    && chown -R postgres:postgres /tempdb \
    && su postgres -c 'pg_ctl -D /tempdb init'

RUN git clone --branch $temporal_tables_version https://github.com/arkhipov/temporal_tables.git /temporal_tables && cd /temporal_tables \
    && make && make install \
    && su postgres -c 'pg_ctl -D /tempdb start' \
    && make installcheck PGUSER=postgres

RUN git clone --branch $jsquery_version https://github.com/postgrespro/jsquery.git /jsquery && cd /jsquery \
    && make USE_PGXS=1 && make install USE_PGXS=1 \
    && su postgres -c 'pg_ctl -D /tempdb start' \
    && make installcheck PGUSER=postgres USE_PGXS=1

ADD --chown=postgres:postgres https://raw.githubusercontent.com/ehrbase/ehrbase/v$ehrbase_version/base/db-setup/createdb.sql /docker-entrypoint-initdb.d/

RUN apk del build-deps && rm -rf /tempdb /jsquery /temporal_tables

ENV PGDATA $PGDATA/ehrbase