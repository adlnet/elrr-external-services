# ELRR External Services
The External Services component of ELRR interacts with external datasources to get data for updates to ELRR Data. Currently the only implemented integration is with an LRS, so it acts as a proxy for LRS read calls.

Typically this proxy is called by [Datasync](https://github.com/adlnet/elrr-datasync) which is a polling process that retrieves the data for processing by ELRR.

## Dev Requirements
- [Java JDK 17](https://www.oracle.com/java/technologies/downloads/) or later
- [Maven](https://maven.apache.org/)

## Tools
- REST Client (such as [Postman](https://www.postman.com/downloads/))
- [Docker](https://www.docker.com/products/docker-desktop/) if working with containers

## Running the Application

### 1. Build the application
`mvn clean install`

This will test, compile, and create a jar for the app in `target/`

### 2a. Running the application using the Spring Boot Maven plugin: 
This is the recommended and easiest way to run a local version of the application

- `mvn clean install`
- `mvn spring-boot:run -D spring-boot.run.profiles=local -e`  (Linux/MacOS)
or
`mvn spring-boot:run -D"spring-boot.run.profiles"=local -e` (Windows)


Note that profile is being set to `local`, this tells spring to leverage `src/main/resources/application-local.properties` which allows you to easily change system settings for your local run. See **Properties and Environment Variables** for details.

### 2b. Running the application uising the Jar file
This is closer to how the application will run in a Docker Container or in production.

- `cd target/`
- `java -jar elrrexternalservices-_.jar` (you must fill in the version number that matches the current target build)

To configure launch for this method you will set ENV variables instead of tweaking `application-local` as the jar will default to `application.properties` which accepts ENV overrides.

## Properties and Environment Variables
Configuration variables for running the application

| Property | ENV Variable | Default | Description |
| -------- | -------- | -------- | -------- |
| lrs.url  | ELRR_LRS_URL | - | URL to access LRS. Should include host, port, protocol and path (e.g. `https://lrs.domain.org/xapi`)
| lrs.username  | ELRR_LRS_USERNAME | - | LRS API Key
| lrs.password  | ELRR_LRS_PASSWORD | - | LRS API Secret
| server.port  | ELRR_PORT | 8088 | Port to deploy on
| check.http.header  | ELRR_LRS_URL | false | Strict header checking
| jasypt.encryptor.password  | JASYPT_ENCRYPTOR_PASSWORD | - | Needed only if using Jasypt `ENC(-)` cyphertext for any of the LRS details above. This is the shared secret for decryption of the value.


## Running Locally With Dependencies and Testing

### System Dependencies

External Services does not depend on other components of ELRR (they depend on it) but it does need a running LRS to properly serve as a proxy. Cloning and running the [ELRR Local Docker Compose](https://github.com/adlnet/elrr-dockercompose) will provide an LRS with credentials matching the current defaults in `application-local.properties`. If you start up this compose, and then run using Spring Boot as detailed above, you should have a running version with a n active connection to the LRS.

### Testing

If you did the prior step, you should be able to visit the following URL and have it display some data from the LRS:

`http://localhost:8088/api/lrsdata?lastReadDate=1970-01-01T00:00:00Z&maxStatements=1000`

If you see an empty json array (`[]`) that just means the LRS is empty, but regardless the system is up and running. If you put some statements in the LRS from the docker compose it should return them at this address.
