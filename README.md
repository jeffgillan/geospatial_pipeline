# OpenDroneMap

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
I am trying to create a pipeline of sequential containers that creates drone imagery products and then does some point cloud analysis. The first container is `opendronemap/odm` and the second container is `jeffgillan/pdal_copc:1.0`. I want outputs from the first container (point clouds .las) to serve as inputs for the second container (some PDAL analysis). I am exploring the use of docker-compose to create this pipeline. 

A `docker-compose.yml` is used to orchestrate this pipeline

Here is my current build, with no idea what is correct or incorrect 
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
