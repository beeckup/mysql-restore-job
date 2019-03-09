# REFACTOR IN PROGRESS

# Sidecar Restore Mysql

## Automatic s3 mysql tar.gz restore

Example deploy on  ```deploy_sidecar_example/docker-compose.yml```

Copy `env.sample` as `.env`

Run ONE TIME ONLY. Restores mysql dump sql inside tar.gz given in .env on existent database

ENVIROMENT VARIABLE   | DESCRIPTION | Values
----------   | ---------- | --------------  
MYSQL_HOST | hostname or ip server mysql | hostname or ip
MYSQL_DATABASE | database name | string
MYSQL_USER | database user | string
MYSQL_PASSWORD | database password | string

## Minio/S3 config

ENVIROMENT VARIABLE   | DESCRIPTION | Values
----------   | ---------- | --------------  
**TAR_TO_RESTORE** | full file path on bucket | string
**TAR_PATH** | directory on bucket (where latest file will be considered) | string
S3_BUCKET | Bucket name | string
S3_HOST | host:port | `host:port`
S3_PROTOCOL | protocol type | `http` or `https`
S3_KEY | key | string
S3_SECRET | secret | string

# Usage with named file

Create `.env` file:

```bash
### db connection
MYSQL_HOST=db
MYSQL_DATABASE=wordpress
MYSQL_USER=root
MYSQL_PASSWORD=123456
TAR_TO_RESTORE=dumpdb/XXXXX.tar.gz
### S3 or minio host
S3_HOST=minio:9000
### Protocol
S3_PROTOCOL=http
### Your bucket name
S3_BUCKET=cicciopollo
### minio or s3 credentials
S3_KEY=85A8U57ZITLSLFBYKNCG
S3_SECRET=14MAuAetrv7y3E6zAuUOimXy5KYRqrZKw3cWuEe/

```

# Usage with latest file from directory

Create `.env` file:

```bash
### db connection
MYSQL_HOST=db
MYSQL_DATABASE=wordpress
MYSQL_USER=root
MYSQL_PASSWORD=123456
TAR_PATH="dumpdb" 
#no trailing slash
### S3 or minio host
S3_HOST=minio:9000
### Protocol
S3_PROTOCOL=http
### Your bucket name
S3_BUCKET=cicciopollo
### minio or s3 credentials
S3_KEY=85A8U57ZITLSLFBYKNCG
S3_SECRET=14MAuAetrv7y3E6zAuUOimXy5KYRqrZKw3cWuEe/

```

Create `docker-compose.yml` file:

```yml
version: '2'
services:
  sidecar-restore-volumes:
      image: beeckup/sidecar-restore-mysql:latest
      restart: "no"
      environment:
        - MYSQL_HOST=${MYSQL_HOST}
        - MYSQL_DATABASE=${MYSQL_DATABASE}
        - MYSQL_USER=${MYSQL_USER}
        - MYSQL_PASSWORD=${MYSQL_PASSWORD}
        - TAR_TO_RESTORE=${TAR_TO_RESTORE} ## or - TAR_PATH=${TAR_PATH}
        - S3_BUCKET=${S3_BUCKET}
        - S3_KEY=${S3_KEY}
        - S3_SECRET=${S3_SECRET}
        - S3_HOST=${S3_HOST}
        - S3_PROTOCOL=${S3_PROTOCOL}
##################
##### example db
  db:
      image: mysql:5.7.22
      restart: always
      ports:
        - "3307:3306" #only test
      environment:
        MYSQL_DATABASE: ${MYSQL_DATABASE}
        MYSQL_ROOT_PASSWORD: ${MYSQL_PASSWORD}

```

Launch with

```bash
docker-compose up -d
```
