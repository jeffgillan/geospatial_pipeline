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
* 



## Random Docker Things
Look at the directory structure inside a docker image
```
docker run --rm -it --entrypoint=/bin/bash opendronemap/odm
```
