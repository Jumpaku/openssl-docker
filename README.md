# openssl-docker

Docker image fo openssl.

## With Docker

Execute as follows:
```sh
docker run -it -v $(pwd):/workdir jumpaku/openssl-docker bash
```

## With Docker Compose

Execute as follows:
```sh
docker-compose run openssl bash
```
with the following `docker-compose.yml`.
```yml
version: '3'

services: 
    openssl:
        image: 'jumpaku/openssl-docker'
        volumes: 
            - './certificates/:/workdir/'
```

## Example

In the above docker container, you can generate self signed certificates as follows:
```sh
CERT_PATH=/workdir
SERVER_NAME=example.com

openssl req -new -newkey rsa:2048 -nodes -out "$CERT_PATH/ca_csr.pem" -keyout "$CERT_PATH/ca_privkey.pem" -subj="/C=JP"
openssl req -x509 -key "$CERT_PATH/ca_privkey.pem" -in "$CERT_PATH/ca_csr.pem" -out "$CERT_PATH/ca_cert.pem" -days 356

openssl req -new -newkey rsa:2048 -nodes -out "$CERT_PATH/csr.pem" -keyout "$CERT_PATH/privkey.pem" -subj="/CN=$SERVER_NAME"
SERIAL="0x$(echo -n $SERVER_NAME | openssl sha256 | awk '{print $2}')"
openssl x509 -req -CA "$CERT_PATH/ca_cert.pem" -CAkey "$CERT_PATH/ca_privkey.pem" -set_serial "$SERIAL" -in "$CERT_PATH/csr.pem" -out "$CERT_PATH/cert.pem" -days 365
```
