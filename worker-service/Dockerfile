# Dockerfile for worker-service

# Use Golang base image
FROM golang:1.20-alpine

# Set the working directory inside the container
WORKDIR /worker-service

# Copy the Go modules and sum files
COPY go.mod go.sum ./

# Download Go modules
RUN go mod download

# Copy the rest of the application code
COPY . .

# Install necessary packages like PostgreSQL client
RUN apk add --no-cache git postgresql-client

# Build the Go application
RUN go build -o worker-service .

# Expose the port that the worker-service listens on
EXPOSE 8084

# Start the worker-service
CMD ["./worker-service"]