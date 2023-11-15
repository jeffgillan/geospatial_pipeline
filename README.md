# Geospatial Pipeline

This repository shows an example of how to use docker and docker compose to create a pipeline for processing and analysis of geospatial data. In this case, a 'pipeline' is defined as a sequence of processing steps that are linked together and automated. With docker, we can create modular scripts that can be run on any computer without having to install software locally. Our example will 1) photogrammetrically process a series of drone images into a point cloud using OpenDroneMap; 2)  Filter the point cloud to separate tree points from ground points using PDAL; 3) Convert the filtered tree points into a canopy height model using an R Script. Each of these steps is an individual docker container. They are orchestrated and ran sequentially using a docker-compose yml configuration file.

## Container 1: opendronemap/odm:3.3.0

Open drone map is a command line tool that will create point clouds, orthomosaics, and DEMs from drone imagery using the SfM workflow. We are using it in this pipeline to create a cloud optimized point cloud (.copc.laz) from a series of drone images found in `/images` in this repository

Details on how to run OpenDroneMap as a docker container are found in this repository https://github.com/jeffgillan/opendronemap. We are using the docker image `opendronemap/odm:3.3.0`


## Container 2: jeffgillan/pdal_csf:1.0


A `docker-compose.yml` is used to orchestrate this pipeline


```
version: "3.8"

services:
  odm:
    image: opendronemap/odm
    container_name: odm
    volumes:
      - ".:/datasets/code"
    command: ["--project-path", "/datasets", "--skip-orthophoto", "--skip-report", "--pc-copc", "--pc-quality", "medium"]

  pdal_copc:
    image: jeffgillan/pdal_csf:1.0
    container_name: pdal_csf
    depends_on:
      odm:
          condition: service_completed_successfully
    volumes:
      - .:/data
```      
The most important part of the docker-compose file is the `depends_on` section which states that the second container will wait to start until the first container finishes. 

`docker-compose up`


## Random Docker Things
Look at the directory structure inside a docker image
```
docker run --rm -it --entrypoint=/bin/bash opendronemap/odm
```
Look at the directory structure inside a running container
```
docker exec -it <container_id_or_name> sh
```
