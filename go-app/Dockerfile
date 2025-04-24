# Use Golang base image
FROM golang:1.19

# Set the working directory in the container
WORKDIR /app

# Copy the Go application code to the container
COPY . .

# Install dependencies
RUN go mod init my-app

# Build the Go application
RUN go build -o myapp .

# Expose the port on which the app will run
EXPOSE 8080

# Run the Go app
CMD ["./myapp"]
