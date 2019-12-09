FROM postgres:10-alpine

RUN apk add --no-cache --virtual build-deps bison flex g++ git make

RUN mkdir /tempdb \
    && chown -R postgres:postgres /tempdb \
    && su postgres -c 'pg_ctl -D /tempdb init'

RUN git clone --branch v1.2.0 https://github.com/arkhipov/temporal_tables.git /temporal_tables && cd /temporal_tables \
    && make && make install \
    && su postgres -c 'pg_ctl -D /tempdb start' \
    && make installcheck PGUSER=postgres

RUN git clone --branch ver_1.1.1 https://github.com/postgrespro/jsquery.git /jsquery && cd /jsquery \
    && make USE_PGXS=1 && make install USE_PGXS=1 \
    && su postgres -c 'pg_ctl -D /tempdb start' \
    && make installcheck PGUSER=postgres USE_PGXS=1

ADD --chown=postgres:postgres https://raw.githubusercontent.com/ehrbase/ehrbase/v0.9.0/base/db-setup/createdb.sql /docker-entrypoint-initdb.d/

RUN apk del build-deps && rm -rf /tempdb /jsquery /temporal_tables

ENV PGDATA $PGDATA/ehrbase