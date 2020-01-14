ARG ehrbase_version=0.9.0
ARG jsquery_version=ver_1.1.1
ARG postgres_version=10
ARG temporal_tables_version=v1.2.0

FROM maven:3.6-jdk-8 as package

ARG ehrbase_version
ARG jsquery_version
ARG postgres_version
ARG temporal_tables_version

WORKDIR /ehrbase

RUN echo 'deb http://apt.postgresql.org/pub/repos/apt/ stretch-pgdg main' >> /etc/apt/sources.list.d/pgdg.list \
    && wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | apt-key add - \
    && apt-get update \
    && apt-get install -y postgresql-$postgres_version postgresql-server-dev-$postgres_version

RUN apt-get install -y build-essential \
    && git clone --branch $temporal_tables_version https://github.com/arkhipov/temporal_tables.git /temporal_tables && cd /temporal_tables \
    && make && make install \
    && chown -R postgres:postgres /temporal_tables && pg_ctlcluster $postgres_version main start && su postgres -c 'make installcheck'

RUN apt-get install -y bison build-essential flex \
    && git clone --branch $jsquery_version https://github.com/postgrespro/jsquery.git /jsquery && cd /jsquery \
    && make USE_PGXS=1 && make USE_PGXS=1 install \
    && chown -R postgres:postgres /jsquery && pg_ctlcluster $postgres_version main start && su postgres -c 'make USE_PGXS=1 installcheck'

RUN git clone --branch v$ehrbase_version https://github.com/ehrbase/ehrbase /ehrbase \
    && pg_ctlcluster $postgres_version main start && cat base/db-setup/createdb.sql | su postgres -c psql \
    && mvn package

FROM openjdk:8-alpine

ARG ehrbase_version

WORKDIR /ehrbase

COPY --from=package /ehrbase/application/target/application-$ehrbase_version.jar ehrbase-$ehrbase_version.jar

CMD ["java", "-Dspring.profiles.active=docker", "-jar", "ehrbase-$ehrbase_version.jar"]