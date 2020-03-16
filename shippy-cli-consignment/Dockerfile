FROM golang:1.13 as builder

WORKDIR $GOPATH/src/shippy-cli-consignment

COPY . .
# ENV GO111MODULE=on
RUN go env

RUN go get -d -v ./...
RUN go install -v ./...

RUN CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o shippy-cli-consignment


FROM alpine:latest

RUN apk --no-cache add ca-certificates

RUN mkdir /app
WORKDIR /app
COPY consignment.json /app/consignment.json
COPY --from=builder /go/src/shippy-cli-consignment .

ENTRYPOINT ["./shippy-cli-consignment", "consignment.json"]
CMD ["./shippy-cli-consignment"]
