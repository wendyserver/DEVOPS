FROM postgres:17.2-alpine

ENV POSTGRES_DB=db \
   POSTGRES_USER=usr \
   POSTGRES_PASSWORD=pwd

# copier les scripts dans le dossier de destination
COPY CreateScheme.sql /docker-entrypoint-initdb.d/
COPY InsertData.sql /docker-entrypoint-initdb.d/