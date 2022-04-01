# Examples

## Setup

vi Dockerfile
```
FROM alpine:3.15.3

WORKDIR /usr/src/app/

ENV AWS_REGION=eu-central-1

RUN apk --update add --no-cache --virtual run-dependencies \
        terraform \
        aws-cli \
        curl


RUN curl -L https://github.com/cycloidio/terracognita/releases/latest/download/terracognita-linux-amd64.tar.gz -o terracognita-linux-amd64.tar.gz \
        && tar -xf terracognita-linux-amd64.tar.gz \
        && rm terracognita-linux-amd64.tar.gz \
        && chmod u+x terracognita-linux-amd64 \
        && mv terracognita-linux-amd64 /usr/local/bin/terracognita
```

Build Docker image
```
docker build -t terracognita:0.1 .
```



Run in Docker
```
docker run -it --rm terracognita:0.1 sh
```



