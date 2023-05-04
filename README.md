# Indexmonitor <img src="https://github.com/IutkinEgor/indexmonitor-assets/blob/main/logo/logo-primary.svg" height="60px">

Indexmonitor is a comprehensive securities market catalog that provides accurate and up-to-date information on the stock market, including prices, company information, and historical data.

## Table of Contents

- [Architecture Overview](#architecture-overview)
- [Clients](#clients)
- [Microservices](#microservices)
- [Data Persistence](#data-persistence)
- [Deployment](#deployment)
- [Networking](#networking)
- [Security](#security)
- [Contributing](#contributing)

## Architecture Overview

![1](https://user-images.githubusercontent.com/60474448/236243927-c5b3a0e3-05d8-4b25-876f-7e80150282b5.png)

_The diagram does not include any container replication for simplicity._

The project follows modern best practices for microservice architecture. While it does not use any orchestrator solution, it fits within the Spring Cloud ecosystem, utilizing Spring Eureka discovery server, Eureka client, and Spring Gateway. Each backend service is represented by a containerized, stateless Spring Boot application. As a result, the application can be scaled manually by configuring any number of replicas.

## Clients

- **Web Client**: The main UI is built on top of Angular 15 with NGINX state management and Angular Material styling engine. To make the application adaptive for any screen size, it uses the Bootstrap 5.0 grid system for its simplicity. It also utilizes the [angular-oauth2-oidc](https://github.com/manfredsteyer/angular-oauth2-oidc) library for user authentication.
- [**Authorization Client**](https://github.com/IutkinEgor/indexmonitor-auth-client): This client provides a comfortable interaction with the Authorization Server. It provides a UI for project OAuth 2.0 management with user, roles, scopes, authorities, and clients management. It is built on top of Angular 15, just like the Web Client.

## Microservices

- [**Authorization Server**](https://github.com/IutkinEgor/indexmonitor-auth-server): This universal auth server provides OAuth 2.1 authorization flow, exposes REST API, and includes built-in MVC templates powered by the Thymeleaf Engine and Bootstrap 5.0 styling.
- **Gateway and Auth Gateway**: The gateway is powered by the Spring Cloud Gateway project.
- **Stock Service**: This service is powered by the Spring Boot and Spring Security projects and behaves as an Eureka client that exposes REST API for securities access and control.
- **Price Service**: This service is also powered by the Spring Boot and Spring Security projects and behaves as an Eureka client that exposes REST API for securities price access and control.
- **Discovery Server**: This server is powered by Spring Cloud Eureka Server and provides high-level integrity between services.

## Data Persistence

For data persistence, PostgreSQL in a container with volume binding is used. While this may be a controversial decision, it is simple and another safe option for the project's goal.

## Deployment

The deployment process in this project is not managed by any CI/CD pipeline. Jenkins was considered for deployment automation, but it was overkill. Renting an additional VPS for deployment purposes exceeds the project's budget. The current deployment process is tag-driven, where each tag denotes a standard version (v0.0.0). When a new tag is created, a new Docker image is built and published to Docker Hub. Finally, the new version is rolled out on the VPS via command.

## Networking

Docker networking is separated into two Docker networks: Public and Private. NGINX proxy, client apps, and gateways are in the same public network, as it is mandatory to be accessible by clients and gateways from the NGINX proxy. Other services are part of the private network and reachable via gateways.

## Security

The VPS is accessible via an NGINX reverse proxy as the main application gateway. It utilizes Let's Encrypt to obtain SSL/TLS certificates for the indexmonitor.info domain.

VPS maintenance is implemented via an SSH connection and SSH tunnel that is protected by Linux user group restrictions and 2FA with Google Authenticator.
