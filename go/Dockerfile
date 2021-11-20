################ Build & Dev ################
# Build stage will be used:
# - for building the application for production
# - as target for development (see devspace.yaml)
ARG debian=alpine
ARG go_version=1.17.3
ARG protobuf_version=3.19.1
ARG protoc_gen_grpc_web_version=1.2.1
ARG grpc_version
ARG grpc_gateway_version
ARG grpc_java_version
ARG uber_prototool_version
ARG scala_pb_version
ARG node_version
ARG node_grpc_tools_version
ARG node_grpc_tools_node_protoc_ts_version 
ARG node_protoc_gen_grpc_web_version
ARG go_envoyproxy_pgv_version

FROM golang:$go_version-$debian AS build

# RUN sed -i s/deb.debian.org/mirrors.aliyun.com/g /etc/apt/sources.list
# RUN sed -i s/security.debian.org/mirrors.aliyun.com/g /etc/apt/sources.list
# RUN sed -i 's/archive.ubuntu.com/mirrors.ustc.edu.cn/g' /etc/apt/sources.list
# RUN sed -i 's/security.ubuntu.com/mirrors.ustc.edu.cn/g' /etc/apt/sources.list
# RUN apt-get clean && apt-get update

RUN sed -i 's/dl-cdn.alpinelinux.org/mirrors.ustc.edu.cn/g' /etc/apk/repositories

# RUN set -ex && apt-get update && apt-get install -y --no-install-recommends \
#     build-essential \
#     curl \
#     git \
#     unzip \
#     cmake 
#     # pkg-config \
#     # libtool \
#     # autoconf \
#     # zlib1g-dev \
#     # libssl-dev \
#     # clang

RUN ["apk","update"]
RUN ["apk","add","curl"]
RUN ["apk","add","unzip"]
RUN ["apk","add","cmake"]

RUN apk update && \
    # apk add --no-cache --update curl  && \
    # apk add --no-cache --update unzip  && \
    # apk add --no-cache --update cmake  && \
    # 清除缓存
    rm -rf /tmp/* /var/cache/apk/*

# Moving to installation workdir
RUN mkdir -p /opt/setup
WORKDIR /opt/setup

# Downloading compatible glibc for alpine
# ADD https://alpine-pkgs.sgerrand.com/sgerrand.rsa.pub /etc/apk/keys/sgerrand.rsa.pub
# ADD https://github.com/sgerrand/alpine-pkg-glibc/releases/download/2.28-r0/glibc-2.28-r0.apk .
# RUN echo "f0a00f56fdee9dc888bafec0bf8f54fb188e99b5346032251abb79ef9c99f079  glibc-2.28-r0.apk" > glibc_sum.txt && \
#     sha256sum -c glibc_sum.txt && \
#     rm glibc_sum.txt
# RUN apk add --no-cache --update \
#     glibc-2.28-r0.apk

# Protobuf
# ENV PROTOC_ZIP=protoc-$protobuf_version-linux-x86_64.zip
ENV   PROTOC_ZIP=protoc-3.19.1-linux-x86_64.zip
RUN curl -OL https://walkman.oss-cn-hangzhou.aliyuncs.com/protocolbuffers/protobuf/${PROTOC_ZIP}
RUN unzip ${PROTOC_ZIP} && \
    chmod +x -R bin/ && \
    mv bin/protoc /usr/bin/ && \
    mv include /usr/include

# Download protoc-gen-grpc-web
# ENV GRPC_WEB=protoc-gen-grpc-web-$protoc_gen_grpc_web_version-linux-x86_64
# ENV GRPC_WEB_PATH=/usr/bin/protoc-gen-grpc-web
# RUN curl -OL https://github.com/grpc/grpc-web/releases/download/$protoc_gen_grpc_web_version/${GRPC_WEB}
# # Copy into path
# RUN mv ${GRPC_WEB} ${GRPC_WEB_PATH} && \
#     chmod +x ${GRPC_WEB_PATH}

# Cleanup
RUN rm -rf /opt/setup

# 为我们的镜像设置必要的环境变量
ENV GO111MODULE=on \
    CGO_ENABLED=0 \
    GOOS=linux \
    GOARCH=amd64 \
	GOPROXY="https://goproxy.cn,direct"

# RUN go get github.com/golang/protobuf/{proto,protoc-gen-go}
# RUN go get github.com/golang/protobuf/proto
RUN go get google.golang.org/protobuf
RUN go get github.com/golang/protobuf/protoc-gen-go
RUN go get -u github.com/go-kratos/kratos/cmd/kratos/v2@latest

# # Build application
# RUN CGO_ENABLED=0 GOOS=linux \
#    go build -a -installsuffix cgo -ldflags '-extldflags "-static"' -o main .

# Create project directory (workdir)
# # Application workdir
WORKDIR /app

# Add source code files to WORKDIR
ADD . .

# Application port (optional)
EXPOSE 8080

# Container start command for development
# Allows DevSpace to restart the dev container
# It is also possible to override this in devspace.yaml via images.*.cmd
CMD ["/bin/sh"]