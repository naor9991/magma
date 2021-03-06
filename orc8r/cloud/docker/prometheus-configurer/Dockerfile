# Golang image to build prom_alertconfig service
FROM golang:1.13-alpine3.11 as go

ENV GO111MODULE on
ENV GOBIN /build/bin
ENV PATH ${PATH}:${GOBIN}
# Use public go modules proxy
ENV GOPROXY https://proxy.golang.org

# Copy go.mod file to cache the downloads
COPY cloud/go/go.mod /gomod/cloud/go/
COPY gateway/go/go.mod /gomod/gateway/go/
COPY lib/go/go.mod /gomod/lib/go/
COPY lib/go/protos/go.mod /gomod/lib/go/protos/
WORKDIR /gomod/cloud/go
RUN go mod download

# Copy magma code
COPY cloud/go/ /go/src/magma/orc8r/cloud/go/
COPY gateway/go/ /go/src/magma/orc8r/gateway/go/
COPY lib/go/ /go/src/magma/orc8r/lib/go/

# Build prom_alertconfig service
WORKDIR /go/src/magma/orc8r/cloud/go/services/metricsd/prometheus/configmanager/prometheus/
RUN go build -i -o $GOBIN/prometheus_configurer

FROM alpine:3.11

COPY --from=go /build/bin/prometheus_configurer /bin/prometheus_configurer

# Copy config files
COPY cloud/docker/prometheus-configurer/configs /etc/configs

ENTRYPOINT ["prometheus_configurer"]
