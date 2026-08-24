FROM postgres:16-alpine

ENV POSTGRES_DB=disco
ENV POSTGRES_USER=disco
ENV POSTGRES_PASSWORD=disco

COPY dump.sql /docker-entrypoint-initdb.d/

EXPOSE 5432
