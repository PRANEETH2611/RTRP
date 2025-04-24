FROM golang:1.24.2

WORKDIR /app

COPY . .

RUN go build -o myapp .

EXPOSE 8080

CMD ["./myapp"]
