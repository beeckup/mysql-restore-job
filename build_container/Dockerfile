FROM golang:alpine
WORKDIR /go/src/app
# install mysql dump curl
RUN apk update \
    && apk add mysql-client \
    && apk add --no-cache openssl curl \
    && apk add bash
RUN apk add python
RUN apk add py-pip
RUN pip install awscli
COPY . .
CMD ["./restore.sh"]