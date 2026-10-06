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
  👉 [http://localhost:8080/scalar](http://localhost:8080/scalar)

- **OpenAPI JSON specification**  
  👉 [https://dataspace.bmdproject.eu/v3/api-docs](https://dataspace.bmdproject.eu/v3/api-docs)

- **Javadocs**

  👉

_(Developer note: the OpenAPI metadata (title, version, description) is defined in
`src/main/java/eu/bmdproject/dataspace/OpenApiConfig.java`. See the
[Springdoc OpenAPI documentation](https://springdoc.org/) for more info.)_

## Developer Setup

## Docker / Docker Compose

