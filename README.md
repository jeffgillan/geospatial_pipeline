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



## Random Docker Things
Look at the directory structure inside a docker image
```
docker run --rm -it --entrypoint=/bin/bash opendronemap/odm
```
