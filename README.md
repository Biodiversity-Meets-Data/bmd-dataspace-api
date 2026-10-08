# BMD Dataspace API

This repository contains the code for interacting with the BMD Dataspace. The BMD Dataspace API
is a REST API implemented in Java/Spring Boot. Its endpoints fall apart into three main categories:
1. Endpoints for interacting with the Cubing Engine running on the BMD Dataspace.
2. Endpoints for ingesting the RO-Crates produced by the BATs. The BMD Dataspace API will inspect
   the RO-Crates and use the information collected from them to populate a triple store. Information
   inside the triple store is exposed via other endpoints in this corner of the BMD Dataspace API.
   Whether the RO-Crates are themselves stored in the BMD Dataspace (or discarded after analysis)
   is currently an open question.
3. General utilities not necessarily tied to the BMD Dataspace. Currently these are focused on
   providing a harmonized view of Natura2000 site metadata and geoshapes from various organizations
   (BISE, EUNIS, EEA).


## Documentation

- **OpenAPI UI (interactive docs)**  
  👉 <https://dataspace.bmdproject.eu/scalar>

- **OpenAPI JSON specification**  
  👉 <https://dataspace.bmdproject.eu/v3/api-docs>

- **Javadocs**  
  👉 [TODO]

_Developer note: the OpenAPI metadata (title, version, description) is defined in
[OpenApiConfig](src/main/java/eu/bmdproject/dataspace/config/OpenApiConfig.java). See the
[Springdoc OpenAPI documentation](https://springdoc.org/) for more info._

## Developer Setup

### Prerequisites

- **JDK 25**
- **Maven**. The project comes with the Maven wrapper (`./mvnw`), which downloads Maven 3.9.9 on first use. 
- **Git**
- **Docker**
- **Natura2000_end2024.gpkg**. Required for the site-geometry endpoints (`/sites/{code}/geojson`). Download it from 
  <https://sdi.eea.europa.eu/data/91357f39-7866-41ce-b447-43905c364ec8> and save it to `${APP_DATA_DIR}/eea` (see 
  [Data Directories](#data-directories))

### Data Directories

The application assumes the presence of an `APP_DATA_DIR` directory — the top directory of all persistent storage
required by the application. You are free to choose the location of APP_DATA_DIR. By default it is assumed to be 
`/data/bmd/`. See [application.yml](src/main/resources/application.yml) and [env.template](env.template). Make sure
`APP_DATA_DIR` is owned by the user who runs the application. Assuming you stick to the default value, create the
following directories:

```bash
mkdir -p /data/bmd/jena /data/bmd/eea
sudo chown -R $USER:$USER /data/bmd
```

As mentioned above, the `eea` subdirectory should contain the Natura2000_end2024.gpkg file. The `jena` subdirectory is
used as the data directory for the Apache Jena triple store.

### Getting the Code

```bash
git clone git@github.com:Biodiversity-Meets-Data/bmd-dataspace-api.git
cd bmd-dataspace-api
```

### Building

```bash
./mvnw clean install
```

A few things worth knowing about the build:

- `install` does more than produce the jar. It copies all runtime dependencies to `build/dependencies/` and the 
  application jar to `build/app/app.jar`. The `Dockerfile` consumes both directories, so the container image is 
  assembled from the output of a normal `install` rather than from a fat jar.
- There is deliberately **no** `spring-boot-maven-plugin`. The project does not build an executable fat jar and 
  `./mvnw spring-boot:run` will not work. See *Running locally* below for how to start the application.
- The enforcer plugin fails the build on dependency-version divergence
  (`DependencyConvergence`). If you add or bump a dependency and the build complains, run `./mvnw dependency:tree` 
  to find the conflict and pin the version in `dependencyManagement`.

To run only the tests:

```bash
./mvnw test
```

Test coverage is measured by JaCoCo. After a test run the HTML report is at `target/site/jacoco/index.html`.

### Running Locally

Because there is no Spring Boot repackaging plugin, run the application by starting the main class directly:

- **From IntelliJ:** run `eu.bmdproject.dataspace.BmdDataspaceApiApplication`.
- **From the command line**, after `./mvnw install`:

  ```bash
  java -cp "build/dependencies/*:build/app/*" eu.bmdproject.dataspace.BmdDataspaceApiApplication
  ```

All persistent paths derive from a single setting, `APP_DATA_DIR` (see [Data Directories](#data-directories)), which 
defaults to `/data/bmd`. At startup the application opens a Jena TDB2 dataset under `${APP_DATA_DIR}/jena`, so that 
directory must exist and be writable, or the application will not start.

```bash
export APP_DATA_DIR=/data/bmd # or whatever location you chose for APP_DATA_DIR
mkdir -p "$APP_DATA_DIR/jena" "$APP_DATA_DIR/eea"
java -cp "build/dependencies/*:build/app/*" eu.bmdproject.dataspace.BmdDataspaceApiApplication
```

In IntelliJ, set `APP_DATA_DIR` under *Environment variables* in the Run Configuration.

The site metadata endpoints (`/sites/{code}/metadata`) call the EEA Discodata service at 
<https://discodata.eea.europa.eu/sql>, so you need network access for those. No credentials are required.

Once started, the application logs the list of discovered HTTP endpoints (see 
[EndpointSummaryLogger](src/main/java/eu/bmdproject/dataspace/bootstrap/EndpointSummaryLogger.java)), a quick way to 
confirm the controllers are wired up.

### Verifying It Runs

With the application running on the default port 8080:

- Health: <http://localhost:8080/api/health>
- Version info: <http://localhost:8080/version>
- OpenAPI UI (Scalar): <http://localhost:8080/scalar>
- OpenAPI JSON: <http://localhost:8080/v3/api-docs>

### Pre-commit hooks

The repository uses [pre-commit](https://pre-commit.com/) to run
[gitleaks](https://github.com/gitleaks/gitleaks) as a secret scanner before each commit.
The hook runs gitleaks via Docker, so Docker must be running.

```bash
pipx install pre-commit   # or: pip install pre-commit
pre-commit install
```

After this, gitleaks runs automatically on `git commit`. To scan the whole tree on demand:

```bash
pre-commit run --all-files
```

## Docker / Docker Compose

[TODO]

## Deploy

[TODO]