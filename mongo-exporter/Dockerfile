# Stage 1
# Download the Mongo Exporter from the Git Repository
FROM alpine/git as git
WORKDIR /app
RUN git clone https://github.com/dcu/mongodb_exporter.git

# Stage 2
# Compile and build the Mongo Exporter
FROM golang:1.14-alpine as builder
COPY --from=git /app/ /go/src/github.com/dcu/

RUN apk --no-cache add curl git make perl
RUN curl 'https://raw.githubusercontent.com/Masterminds/glide.sh/master/get' -o /tmp/get_glide && \
			sed -i 's+get TAG https://glide.sh/version+TAG="v0.13.3"+g' /tmp/get_glide && \
			sh /tmp/get_glide
RUN cd /go/src/github.com/dcu/mongodb_exporter && make release

# Stage 3
# Bundle the final Mongo Export Container Image
FROM alpine
ARG MONGO_SERVICE
ARG MONGO_PORT
ENV MONGODB_URL "mongodb://${MONGO_SERVICE}:${MONGO_PORT}"
RUN apk add --update ca-certificates
COPY --from=builder /go/src/github.com/dcu/mongodb_exporter/release/mongodb_exporter-linux-amd64 /usr/local/bin/mongodb_exporter
EXPOSE 9001
ENTRYPOINT [ "mongodb_exporter" ]