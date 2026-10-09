# BMD Dataspace — Architecture

## List of Abbreviations and Acronyms

|     |                             |
|:----|:----------------------------|
| BAT | Biodiversity Analysis Tools |
| BMD | Biodiversity Meets Data     |
| SDM | Species Distribution Model  |
| WP  | work package                |

## 1. Introduction

[Biodiversity Meets Data](https://bmd-project.eu/) (BMD) is an EU-funded project that aims to provide biodiversity
monitoring for Natura 2000 managers (the stakeholders). Its end product is a portal, the Biodiversity Explorer, in which
they can run species distribution models (SDMs). An SDM relates observed occurrences of a species to environmental
variables such as temperature and precipitation, and uses that relationship to estimate where the species occurs, or
could occur, under past, current or projected conditions.

BMD is split into eight work packages (WPs), each with their own deliverables. The BMD Dataspace is built in WP4. The
work packages most relevant for this document are listed below, in the order in which data flows through them.

* **WP3. The Cubing Engine.** The Cubing Engine turns heterogeneous source data (climate and satellite rasters, GBIF
  occurrence records, ...) into data cubes. A data cube is a multidimensional array in which each value is addressed by
  where (a grid cell), when (a point in time) and what (a species, or a climate variable such as temperature). An
  occurrence cube, for example, holds the number of observations per grid cell, per year, per species; a climate cube
  holds, say, the mean temperature per grid cell per month. Because all sources share the same grid and coordinate
  reference system, they can be compared and combined directly. Cubes are stored in cloud-optimized formats, so that an
  analysis can read just the slice it needs from very large datasets.
* **WP4. The BMD Dataspace.** The topic of this document.
* **WP5. The Biodiversity Analysis Tools (BATs).** The BATs provide the machinery to run the species distribution
  models. Each BAT also has its own frontend. For each type of ecosystem or "realm" a separate BAT is being developed
  (land species, marine species, ...). The BATs run on LifeWatch infrastructure, not on the dataspace; they obtain their
  input by requesting cubes from the dataspace.
* **WP6. The Biodiversity Explorer.** The portal through which Natura 2000 managers reach the BATs. There is no direct
  data exchange between the Explorer and the dataspace. Whether the Explorer also provides authentication and
  authorization for the dataspace is not yet settled.