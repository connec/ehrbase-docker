FROM maven:3.6-jdk-8 as package

WORKDIR /ehrbase

RUN echo 'deb http://apt.postgresql.org/pub/repos/apt/ stretch-pgdg main' >> /etc/apt/sources.list.d/pgdg.list \
    && wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | apt-key add - \
    && apt-get update \
    && apt-get install -y postgresql-10 postgresql-server-dev-10

RUN apt-get install -y build-essential \
    && git clone --branch v1.2.0 https://github.com/arkhipov/temporal_tables.git /temporal_tables && cd /temporal_tables \
    && make && make install \
    && chown -R postgres:postgres /temporal_tables && pg_ctlcluster 10 main start && su postgres -c 'make installcheck'

RUN apt-get install -y bison build-essential flex \
    && git clone --branch ver_1.1.1 https://github.com/postgrespro/jsquery.git /jsquery && cd /jsquery \
    && make USE_PGXS=1 && make USE_PGXS=1 install \
    && chown -R postgres:postgres /jsquery && pg_ctlcluster 10 main start && su postgres -c 'make USE_PGXS=1 installcheck'

RUN git clone --branch v0.9.0 https://github.com/ehrbase/ehrbase /ehrbase \
    && pg_ctlcluster 10 main start && cat base/db-setup/createdb.sql | su postgres -c psql \
    && mvn package

FROM openjdk:8-alpine

WORKDIR /ehrbase

COPY --from=package /ehrbase/application/target/application-0.9.0.jar ehrbase-0.9.0.jar

RUN mkdir -p file_repo/knowledge/archetypes file_repo/knowledge/operational_templates file_repo/knowledge/templates

CMD ["java", "-Dspring.profiles.active=docker", "-jar", "ehrbase-0.9.0.jar"]