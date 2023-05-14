# Indexmonitor <img src="https://github.com/IutkinEgor/indexmonitor-assets/blob/main/logo/logo-primary.svg" height="60px">

[indexmonitor.info](https://indexmonitor.info/) is a comprehensive securities market catalog that provides accurate and up-to-date information on the stock market, including prices, company information, and historical data.

__The project’s primary goal__ was to go through all development stages, from idea to release, and deploy it on a VPS under my domain. The project is based on microservice architecture, contains multiple containerized services. I gained a solid understanding of the layers involved in setting up an application in a production environment, including Linux-based VPS, NGINX proxy, DNS name, server security management.

## Table of Contents
- [History](#history)
- [Architecture Overview](#architecture-overview)
- [Clients](#clients)
- [Microservices](#microservices)
- [Data Persistence](#data-persistence)
- [Deployment](#deployment)
- [Networking](#networking)
- [Security](#security)

## History
The project was initially created to practice new programming skills and best practices and it has gone through several iterations since its inception in the summer of 2021.

The first release in August 2021 was built on C# + .NET Core 3.1 and featured a monolithic back-end with an Angular 11 client.

In December 2021, the project moved to .NET 5.0 with separated layers, incorporating significant changes, such as adding base OAuth 2.0 with Identity Server 4, cleaning up the API according to REST standards, and incorporating Angular Material as the main style engine on the client. Additionally, the project's client saw the first usage of ChartJS and NgRX. However, at this point, the client was not adaptive for different screen sizes.

From January 2022, the project was on hold for six months due to my decision to switch from C# + .NET to Java + Spring. In the summer of 2022, the project moved to a new platform with the first attempt at microservices architecture. Many features were lost in the transition, but the resulting code was cleaner and aligned with the current stack.

In November 2022, the primary focus was on developing a custom OAuth2 server with a client for management. The project switched to the own OAuth Server, built on top of Spring Security, in March 2023. During this time, many other services underwent significant changes and improvements.

In April 2023, the first public release was made under the domain indexmonitor.info on a VPS. Before release, the main client was also refactored with complete NgRx utilization and now has screen-adaptive pages. The current goal is to improve test coverage and utilize orchestration solution

## Architecture Overview
![2](https://github.com/IutkinEgor/indexmonitor/assets/60474448/ca17ee35-1e91-4276-a89f-cdab62a9ed85)

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
- **News Server**

## Data Persistence

For data persistence, PostgreSQL in a container with volume binding is used. While this may be a controversial decision, it is simple and another safe option for the project's goal.

## Deployment

The deployment process in this project is not managed by any CI/CD pipeline. Jenkins was considered for deployment automation, but it was overkill. Renting an additional VPS for deployment purposes exceeds the project's budget. The current deployment process is tag-driven, where each tag denotes a standard version (v0.0.0). When a new tag is created, a new Docker image is built and published to Docker Hub. Finally, the new version is rolled out on the VPS via command.

## Networking

Docker networking is separated into two Docker networks: Public and Private. NGINX proxy, client apps, and gateways are in the same public network, as it is mandatory to be accessible by clients and gateways from the NGINX proxy. Other services are part of the private network and reachable via gateways.

## Security

The VPS is accessible via an NGINX reverse proxy as the main application gateway. It utilizes Let's Encrypt to obtain SSL/TLS certificates for the indexmonitor.info domain.

VPS maintenance is implemented via an SSH connection and SSH tunnel that is protected by Linux user group restrictions and 2FA with Google Authenticator.
