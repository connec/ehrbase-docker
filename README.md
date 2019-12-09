# ehrbase-docker

This repository contains a docker-compose file for running an [EHRbase] instance, as well as Dockerfiles for building:

- An image with the EHRbase application ([`ehrbase/Dockerfile`]).
- A PostgreSQL image with the required extensions available ([`ehrbase-postgres/Dockerfile`]).

**Note:** The artefacts in this repository are intended to simplify developing against a locally running EHRbase and are not intended to be used in production environments!

## Usage

```
$ docker-compose up
```

[EHRbase]: https://github.com/ehrbase/ehrbase
[`ehrbase/Dockerfile`]: ehrbase/Dockerfile
[`ehrbase-postgres/Dockerfile`]: ehrbase-postgres/Dockerfile
