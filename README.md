# Geospatial Pipeline

This repository shows an example of how to use docker and docker compose to create a pipeline for processing and analysis of geospatial data. In this case, a 'pipeline' is defined as a sequence of processing steps that are linked together and automated. With docker, we can create modular scripts that can be run on any computer without having to install software locally. Our example will 1) photogrammetrically process a series of drone images into a point cloud using OpenDroneMap; 2)  Filter the point cloud to separate tree points from ground points using PDAL; 3) Convert the filtered tree points into a canopy height model using an R Script. Each of these steps is an individual docker container. They are orchestrated and ran sequentially using a docker-compose yml configuration file.

## OpenDroneMap

Open drone map is a command line tool that will create point clouds, orthomosaics, and DEMs from drone imagery using the SfM workflow

https://docs.opendronemap.org/

The easiest way to run ODM is to run it from an existing docker image which is found on dockerhub
`opendronemap/odm`


```
docker run -ti --rm -v /home/jgillan/Documents/PVCC_hole17/green:/datasets/code  opendronemap/odm --project-path /datasets --skip-orthophoto --skip-report --pc-las --pc-copc 
```

This is what the command is doing:

* Run docker
* Not sure what flags `-it` do exactly
* `--rm` removes image after is has been run
* `-v` mounts a volume. In this case, it is directory on my local machine (/home/jgillan/Documents/PVCC_hole17/green). Within this directory there must be a sub-directory called `/images`. This is where ODM will look for all of the raw drone images. 
* The directory is mounted to the directory `/datasets/code` inside the image
* `opendronemap/odm` is the name of the Docker image which is located on Dockerhub
* The rest of the arguments are flags and options. These are parameters to change how the processing is done. By default, ODM will run through the entire processing pipeline unless you say otherwise. Check out options at https://docs.opendronemap.org/arguments/

To run ODM with very quickly with low quality:
```
docker run -ti -v /home/jgillan/Documents/PVCC_hole17/green:/datasets/code  opendronemap/odm --project-path /datasets --skip-orthophoto --skip-report --pc-las --skip-3dmodel --pc-quality lowest
```


## Pipeline Exploration
I have created a pipeline of sequential containers that creates drone imagery products and then does some point cloud analysis. The first container is `opendronemap/odm` and the second container is `jeffgillan/pdal_copc:1.0`. I want outputs from the first container (point clouds .las) to serve as inputs for the second container (using PDAL to convert .las to .copc). 

A `docker-compose.yml` is used to orchestrate this pipeline


```
version: "3.8"

services:
  odm:
    image: opendronemap/odm
    container_name: odm
    volumes:
      - "/home/jgillan/Documents/PVCC_hole17/green:/datasets/code"
    command: ["--project-path", "/datasets", "--skip-orthophoto", "--skip-report", "--pc-las", "--skip-3dmodel", "--pc-quality", "lowest"]

  pdal_copc:
    image: jeffgillan/pdal_copc:1.0
    container_name: pdal_copc
    depends_on:
      odm:
          condition: service_completed_successfully
    volumes:
      - /home/jgillan/Documents/PVCC_hole17/green/odm_georeferencing:/data
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
