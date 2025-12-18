# Multi-stage Dockerfile for .NET 10.0 optimized for Podman
# Podman-compatible Dockerfile - works with both Docker and Podman
# For Apple Silicon (ARM64), build with: podman build --platform linux/arm64

# Base runtime image
FROM mcr.microsoft.com/dotnet/aspnet:10.0 AS base
# Copy and update certificates as root before switching to app user
COPY nginx/api-cert.crt /usr/local/share/ca-certificates/api-cert.crt
RUN update-ca-certificates
# Now switch to app user
USER app
WORKDIR /app
EXPOSE 8080
EXPOSE 8081

# Build stage
FROM mcr.microsoft.com/dotnet/sdk:10.0 AS build
ARG BUILD_CONFIGURATION=Release
WORKDIR /src

# Copy project file and restore dependencies (layer caching optimization)
COPY ["ConferenceAttendees.MVC/ConferenceAttendees.MVC.csproj", "ConferenceAttendees.MVC/"]
RUN dotnet restore "./ConferenceAttendees.MVC/ConferenceAttendees.MVC.csproj"

# Copy remaining source files
COPY . .
WORKDIR "/src/ConferenceAttendees.MVC"

# Build the project
RUN dotnet build "./ConferenceAttendees.MVC.csproj" -c $BUILD_CONFIGURATION -o /app/build

# Publish stage
FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "./ConferenceAttendees.MVC.csproj" -c $BUILD_CONFIGURATION -o /app/publish /p:UseAppHost=false

# Final runtime image
FROM base AS final
WORKDIR /app

# Copy published application from publish stage
# Using --chown for Podman rootless compatibility
COPY --chown=app:app --from=publish /app/publish .

# Set entry point
ENTRYPOINT ["dotnet", "ConferenceAttendees.MVC.dll"]