# CKAN para senasa 

Este repositorio genera imagenes de ckan para senasa. Creando un tag, crea una
imagen nueva

## Como se configura y usa esta imagen

La imagen instala dos plugins:

* El tema de senasa
* [ckanext-envvars](https://github.com/okfn/ckanext-envvars)

El último de los plugins mencionados, se instala para permitir configurar Ckan
desde variables de ambiente.

## Ejemplo de docker-compose

```yml
version: '2'
volumes:
  ckan-solr:
  ckan-data:
  ckan-db:
services:
  solr:
    image: ckan/solr:latest
    volumes:
    - ckan-solr:/opt/solr/server/solr/ckan/data
  datapusher:
    image: clementmouchet/datapusher
  ckan:
    image: mikroways/ckan-senasa:1.0.0
    environment:
      CKAN__SITE_LOGO: /logo.png
      CKAN__LOCALE_DEFAULT: es
      CKAN__LOCALES_OFFERED: es en pt_BR fr it
      CKAN_DATAPUSHER_URL: http://datapusher:8800
      CKAN_LOCALE_DEFAULT: es
      CKAN_REDIS_URL: redis://redis:6379/1
      CKAN_SITE_URL: http://ckan.senasa.gob.ar/
      CKAN_SOLR_URL: http://solr:8983/solr/ckan
      CKAN_SQLALCHEMY_URL: postgresql://ckan:ckan@db:5432/ckan
    ports:
      - 5000:5000
    volumes:
    - ckan-data:/var/lib/ckan
      #    - ckan-config:/etc/ckan
      #    - ckan_home:/usr/lib/ckan
    links:
    - redis:redis
    - db:db
    - solr:solr
  redis:
    image: redis:3-alpine
  db:
    image: postgres:9.6
    environment:
      PGDATA: /var/lib/postgresql/data/ckan
      POSTGRES_DB: ckan
      POSTGRES_PASSWORD: ckan
      POSTGRES_USER: ckan
    volumes:
    - ckan-db:/var/lib/postgresql/data/ckan

```

Luego, correr de la siguiente forma:

```
docker-compose -p ckan-senasa up -d
```

Para probar, acceder a http://localhost:5000
