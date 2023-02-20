# Week 1 — App Containerization

Created docker images & dockerfile
- Practical experience in taking a docker image from a public registry and pulling to my local docker host. As part of the same command, I've run it on the same container
- Docker files are a set of steps instructions or directives used via the docker build command to build an image. This image can be used to run a container. 
- I've learnt the theory of ducker files, as well as gained practical experience in creating & using them to create docker images. 
-  Comments start with #
-  Lines have instruction & arguments. It's instruction to have instructions in ALL CAPS to distinguish from arguments:
FROM - the FROM instruction configures which base image is used as the foundation for the image to be created.  sets the base image for a build, for example Apline which is lightweight ideal for Docker (5mb). Very efficient. Can specify an image name & version tag, e.g. Alpine Latest. 
LABEL - can be used to add metadata to an image such as description and wh omaintains the image
The blow create layers:
RUN - executes commands within a new layer in an image. Typicalluy used to install packages applications test suits or other config. Any output of this RUN instruction will be stored on a new layer within the docker image. 
COPY - CCopies NEW files/folders from src (client machine) to destination (new image layer)
ADD - as above, but can add from a remote URL & do exctraction etc (e.g. addng application/web files). They add files to a new layer within your image. 

CMD - defdines the default executable of a container. So if you create a container you can state using CMD that you want to run a certain game or web server. If you don't specify any arguments within the command line then the default argument is chosen, otherwise overridden with CMD to set the executable of a container & arguments
ENTRYPOINT - As above, can't be overridden in the same way. As a general rule, use CMD when creating general purpose container image where you might want to allow different executables to be run within containers. Whereas use ENTRYPOINT when you want a specific single purpose container image. otherwise similiar. Can be used togetherwith CMD providing defualt options for the executable specified in the entrypoint instruction
EXPOSE - informs Docker what port the container app is running on (metadata only - no network configuration!) So if web app running via web server on port TCP port 80 then that app would be running on that container on that port and expose is a way of telling docker which port that container is running on. Tells Docker what port a containerised application is running on inside a container. 

Docker file and build process. First it downloads NginX:latest image because I don't have it locally. Since it's specified in FROM directive in Dockerfile it downloads it to my local docker host because it's the base image. It's been extracted to my local host and the first layer. Then the COPY command copies anything from within the 2048 folder to the /usr/share/nginx/html folder inside my image. It's a new file system layer. 
Can test whether it's running with "docker images" *docker images*

To run a container from this image, I need to run a Docker RUN command. The containerised app is running a service on port 80 so it needs to map port 80 fron the container to a different port on docker host specified on Docker RUN command "Docker run -d (detatch our terminal from the container so we can strill interact with termina; ) -p (configure port mapping) 8081(local):80(container) + specify docker image to be used 
*docker 1* Run a container from the dockerimage just created. 

*docker 3* running docker ps to verify that the container is running, when it was created along with the status. 
*docker 4* docker port + containerid can verify that port 80/tcp on container has been mapped on 8081 on my dockerhost. 
*docker 5* running on localhost:8081 


STRETCH I deployed a rickroll image on my dockerhost for a sweet Rickroll opportunity. *docker 6*



Docker container storage 
- Writable layer. Let's say we have a docker image with 3 layers, conceptually it'll be represented as a single file system. When you run a container, a writable layer is added to this using the union file system. The result is that you see all of the existing data from all file system layers in the image and any new data is stored within this writable layer incluidng both data which is overwritten and any new data. 
Unmion file system allows files and directories from separate file systems to be overlaid to form a single file system. This is what allows this writable layer to be added over the read only layers of the docker image. This writable layer is what makes every container it's own unique thing. This architecture requires a file system driver which has overhead and impacts performance - a consideration for architecting. 
Layers from the docker image can be read from but any writes go into the writable layer and the 

tmpfs is file system accessible to specific container which uses memory in the docker host. Simply put, it's fast ephemeral storage - not stored on disk. Cant be shared between containers. Use it for sensitive files which don't want to be stored on disk for any length of time, holding for short term while processing. 
Bind Mount - Type of storage. In addition to containers running on docker host, also have host's file system. here we have root inside folders called /tmp, /srv and some others. bind mount allow to take folder on host's file system and mount inside container. one folder on host's file system can be accessed by multiple containers running on same host. Good type of storage if need to share storage stored on host. But it means this ype of storage and containers that rely on this rely on a certain structure existing on the host which can reduce portability. Bind mounts, ebcause they exist as folders outside containers, will live on past lifetime of that containers. And the fodlers that are used on the host could themselves be network based folders foe example mounted remotely using NFS. 
Volumes - are like bind mounts in that they are storage accessibel to a container but are managed by docker. So volume is created by docker as required and live outside lifecycle of a container which means they remain after a container is removed. Can be moved between containers and used by multiple containers though there's no file locking so have to be careful. 


# Docker container networking 
At this level there are 2 modes of docker networkking - host networking & bridge networking. 

Docker storage - Bind mounts. map folders within a container to folder on docker hosts. Named volumes - storage managed by docker which can persist past the lifetime of any single container. 

