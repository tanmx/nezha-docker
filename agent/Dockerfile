FROM golang:alpine AS binarybuilder
RUN apk --no-cache --no-progress add \
    gcc git musl-dev

ENV GO111MODULE=on
ENV CGO_ENABLED=0
ARG VERSION
ARG TARGETARCH
WORKDIR /home

RUN git clone https://github.com/naiba/nezha.git &&\
    cd nezha && VERSION=$(git describe --tags `git rev-list --tags --max-count=1`) &&\
    git checkout $VERSION &&\
    cd cmd/agent && go build -o nezha-agent -trimpath -ldflags="-s -w -X main.version=${VERSION:1} -X main.arch=${TARGETARCH}"


FROM alpine:latest

RUN apk --no-cache --no-progress add \
    ca-certificates \
    tzdata
ENV TZ="Asia/Shanghai"
RUN cp "/usr/share/zoneinfo/$TZ" /etc/localtime && echo "$TZ" > /etc/timezone 

WORKDIR /agent
COPY --from=binarybuilder /home/nezha/cmd/agent/nezha-agent ./nezha-agent

# USER nobody

ENTRYPOINT ["/agent/nezha-agent", "--skip-procs", "--disable-command-execute", "--report-delay", "3"]
