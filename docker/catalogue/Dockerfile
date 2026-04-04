FROM golang:1.7

ARG user=app
ARG group=app
ARG uid=1000
ARG gid=1000

RUN mkdir /app
COPY . /go/src/github.com/microservices-demo/catalogue/
COPY images/ /images/

RUN go get -u github.com/FiloSottile/gvt
RUN cd /go/src/github.com/microservices-demo/catalogue && gvt restore

RUN CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o /app/main github.com/microservices-demo/catalogue/cmd/cataloguesvc

RUN groupadd -g ${gid} ${group} \
    && useradd  -u ${uid} -g ${gid} -m -s /bin/bash ${user}


RUN chown -R ${user}:${group} /app/main /images

USER ${user}

CMD ["/app/main", "-port=8081", "-images=/images/"]

EXPOSE 8081

