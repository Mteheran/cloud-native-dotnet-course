# Conference Attendees

## Overview

Conference Attendees is an educational project designed to support the course "ASP.NET Core - Cloud-Native App Development." This project demonstrates the integration and deployment of a complex application using Docker Compose and Kubernetes. The application features an API, an MVC client application, a Microsoft SQL Server database, and Seq service for logging, all of which are containerized. Additionally, it includes instructions for setting up an NGINX reverse proxy with DNS configuration.

## Architecture

- **API**: Serves as the backend, handling business logic and data access.
- **MVC Client Application**: Frontend application providing user interface.
- **Microsoft SQL Server**: Database server for data storage.
- **Seq Service**: Used for logging and monitoring the application.
- **NGINX Reverse Proxy**: Manages external access to the internal services with DNS setup.

## Prerequisites

- Docker
- Kubernetes
- Docker Compose
- A domain name for setting up DNS with NGINX

## Setup Instructions

### Step 1: Clone the Repository

```bash
git clone https://github.com/yourusername/ConferenceAttendees.git
cd ConferenceAttendees
```
Step 3: Build and Run with Docker Compose
```bash
docker-compose up --build
```
This command will build the images for each service and run the containers.

Step 4: Deploy with Kubernetes
After testing with Docker Compose, you can deploy the application on Kubernetes:

```bash
kubectl apply -f k8s/.
```
This will set up all necessary deployments, services, and ingress configurations.

Step 5: NGINX and DNS Configuration
Configure NGINX as a reverse proxy. You'll need to modify the provided NGINX configuration files with your domain and set up DNS to point to your server's IP.

Usage
Once deployed, you can access the MVC client by navigating to http://yourdomain.com in your web browser. The API can be accessed via http://api.yourdomain.com.

Contributing
Contributions are welcome! Please fork the repository and submit pull requests to the main branch. For major changes, please open an issue first to discuss what you would like to change.


## individual container build

--macOS

dotnet publish --os linux --arch arm64 /t:PublishContainer -c Release


podman run -p 5000:8080 -p 5001:8081 --rm localhost/conference-attendees-api

-- With variables
podman run -d \
  --name conference-attendees-api-container \
  --platform linux/arm64 \
  -p 5000:8080 \
  -e DOTNET_RUNNING_IN_CONTAINER=true \
  -e ASPNETCORE_HTTP_PORTS=8080 \
  -e ASPNETCORE_URLS=http://+:8080 \
  -e ASPNETCORE_ENVIRONMENT=Development \
  localhost/conference-attendees-api:latest


  -- with connection string:
  podman run -d \
  --name conference-attendees-api-container \
  --platform linux/arm64 \
  -p 5000:8080 \
  -e DOTNET_RUNNING_IN_CONTAINER=true \
  -e ASPNETCORE_HTTP_PORTS=8080 \
  -e ASPNETCORE_URLS=http://+:8080 \
  -e ASPNETCORE_ENVIRONMENT=Development \
  -e ConnectionStrings__ConferenceAttendeeDatabaseConnection="Host=192.168.100.60;Port=5432;Database=ConferenceAttendeeDb;Username=postgres;Password=12345" \
  localhost/conference-attendees-api:latest

  podman run -d --name conference-attendees-api-container --platform linux/arm64 --network conference-network -p 5000:8080 -e DOTNET_RUNNING_IN_CONTAINER=true -e ASPNETCORE_HTTP_PORTS=8080 -e ASPNETCORE_URLS=http://+:8080 -e ASPNETCORE_ENVIRONMENT=Development -e ConnectionStrings__ConferenceAttendeeDatabaseConnection="Host=postgres-container;Port=5432;Database=ConferenceAttendeeDb;Username=postgres;Password=12345" localhost/conference-attendees-api:latest

  podman run -d --name conference-attendees-api-container --platform linux/arm64 --network conference-network -p 5000:8080 -e DOTNET_RUNNING_IN_CONTAINER=true -e ASPNETCORE_HTTP_PORTS=8080 -e ASPNETCORE_URLS=http://+:8080 -e ASPNETCORE_ENVIRONMENT=Development -e ConnectionStrings__ConferenceAttendeeDatabaseConnection="Host=postgres-container;Port=5432;Database=ConferenceAttendeeDb;Username=postgres;Password=12345" localhost/conference-attendees-api:latest


-- Commit to create a custom image
podman commit -f docker --message "Seeded ConferenceAttendeeDb" --author "mtehe" postgres conferenceattendees-postgres:seeded



---- Kubernetes ---
kubectl apply -f admin-user.yml

kubectl apply -f admin-user-cluster-role.yml

-- Create token
kubectl -n kubernetes-dashboard create token admin-user

-- always show the default, we need to specify the namespace
kubectl get all --namespace=cloud-native-dev      