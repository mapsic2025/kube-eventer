FROM public.ecr.aws/docker/library/golang:1.19.3 AS build-env
ADD . /src/github.com/AliyunContainerService/kube-eventer
ENV GOPATH /:/src/github.com/AliyunContainerService/kube-eventer/vendor
ENV GO111MODULE on
ENV GOPROXY=https://goproxy.cn,direct
WORKDIR /src/github.com/AliyunContainerService/kube-eventer
RUN apt-get update -y && apt-get install gcc ca-certificates
RUN make


FROM public.ecr.aws/docker/library/alpine:3.21

RUN sed -i 's#https\?://dl-cdn.alpinelinux.org/alpine#https://mirrors.tuna.tsinghua.edu.cn/alpine#g' /etc/apk/repositories && \
    apk --no-cache --update upgrade

COPY --from=build-env /src/github.com/AliyunContainerService/kube-eventer/kube-eventer /
COPY --from=build-env /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/

ENV TZ "Asia/Shanghai"
RUN apk add --no-cache tzdata
#COPY deploy/entrypoint.sh /
RUN addgroup -g 1000 nonroot && \
    adduser -u 1000 -D -H -G nonroot nonroot && \
    chown -R nonroot:nonroot /kube-eventer
USER nonroot:nonroot

ENTRYPOINT ["/kube-eventer"]

