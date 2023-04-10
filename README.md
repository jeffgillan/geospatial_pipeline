# OpenDroneMap

Open drone map is a command line tool that will create point clouds, orthomosaics, and DEMs from drone imagery using the SfM workflow

https://docs.opendronemap.org/

The easiest way to run ODM is to run it from an existing docker image which is found on dockerhub

```
docker run -ti --rm -v /home/jgillan/Documents/PVCC_hole17/green:/datasets/code  opendronemap/odm --project-path /datasets --skip-orthophoto --skip-report --pc-las --pc-copc 
```
