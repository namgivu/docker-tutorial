workshop hosted by `Tuan A. VUONG`

# Run a container
```
docker run -d                            --name mongo        mongo
           # run as background daemon    # container name    # image name at hub.docker.com/_/mongo
```
docker image to be `mongo` aka `mongo:latest` ie in the form of `name:tag`
`tag` is similar to **git tag**


# `0` image files path
docker image path
on my machine, at what path do storing the pulled docker images? 

docker config file  `/etc/docker/key.json`

downloaded files at `/var/lib/docker`
also get it by 
```
docker info | grep 'Docker Root Dir'
 Docker Root Dir: /var/lib/docker
```

can also view path info in `docker info`
```
Client:
 Debug Mode: false

Server:
 Containers: 27
  Running: 8
  Paused: 0
  Stopped: 19

 Images: 41

 Server Version: 19.03.3

 Storage Driver: overlay2
  Backing Filesystem: extfs
  Supports d_type: true
  Native Overlay Diff: true

 Logging Driver: json-file
 Cgroup Driver: cgroupfs
 Plugins:
  Volume: local
  Network: bridge host ipvlan macvlan null overlay
  Log: awslogs fluentd gcplogs gelf journald json-file local logentries splunk syslog
 Swarm: inactive
 Runtimes: runc
 Default Runtime: runc

 Init Binary: docker-init

 containerd version: b34a5c8af56e510852c35414db4c1f4fa6172339
 runc version: 3e425f80a8c931f88e6d94a8c831b9d5aa481657
 init version: fec3683

 Security Options:
  apparmor
  seccomp
   Profile: default

 Kernel Version: 4.15.0-65-generic
 Operating System: Ubuntu 18.04.3 LTS
 OSType: linux
 Architecture: x86_64
 CPUs: 8
 Total Memory: 7.251GiB
 Name: namgivu-XPS-13-9370
 ID: D7KR:UIGO:OS2J:K2VV:J4W6:2MI7:PKG3:CJHM:SLZV:3ZOK:ZICW:GWRF

 Docker Root Dir: /var/lib/docker

 Debug Mode: false
 Registry: https://index.docker.io/v1/
 Labels:
 Experimental: false
 Insecure Registries:
  127.0.0.0/8
 Live Restore Enabled: false
```

# `1`
why call `daemon` a `running background process`?


# `2`
what create the log when running `docker log :container_name` ?

linux 101
a linux process always has IOStream
stdin --> process --> stdout --> docker will redirect the stdout to the container's file  - TODO where exactly?
                      stderr
    
                      
# `3` container status running, terminated, deleted

# `4` how a container can be a host-OS process and act as an guest-OS?
ref. NN
linux kernel

ref. Tuan
linux namespace cgroups

In MacOS/Windows they will need to run a virtual Linux machine to have the feature of docker.

Most recent docker, they MacOS have had their own cgroups?
ref. https://stackoverflow.com/a/55085366/248616
> So it's not so different from the old days Docker Machine + VirtualBox + boot2docker,
> for the new days, it's just the provisioning is done internally by "Docker for Mac" and VirtualBox is replaced by Apple's Hyperkit, and the "default VM" is a bit more tucked away.

Most recent docker, they Windows have had their own cgroups?
ref. https://www.hanselman.com/blog/DockerAndLinuxContainersOnWindowsWithOrWithoutHyperVVirtualMachines.aspx
> With the latest version of Windows 10 (or 10 Server) and the beta of Docker for Windows, there's **native Linux Container support on Windows 10**. 
  That means there's no Virtual Machine or Hyper-V involved (unless you want), so Linux Containers run on Windows itself using Windows 10's built in container support.
  


# `5` view consumed cpu/memory of a container 
```
docker stats  # similar to htop
```

# `6` run demo app
```bash
# run mongo at port 8888
: prompt1
docker run -d --name mongo -p 8888:27017 mongo

# run demo api app and use mongo at configured 8888 port
: prompt2
cd /path/to/docker-tutorial
    yarn install
    MONGODB_PORT=8888 yarn start
```

note 
env var MONGODB_PORT is loaded in db.js
```js
const port = process.env.MONGODB_PORT || 27017;
```

then you should try 
`try01` docker stop mongo then restart - query `http :8000/list` will see mongo data untouched
`try02` docker rm   mongo then restart - query `http :8000/list` will see mongo data gone
--> save mongo data by using **docker volume mapping**
--> use docker-compose for volume setting
```bash
# clear the current
docker rm -f mongo

# run the api with the new mongo
cd /path/to/docker-tutorial
    docker-compose up
    yarn install
    yarn start
```

    volumes:                            #Volume mount
      - "./data/mongo:/data/db"


# `7` verified original author of a docker image
mongo image is hosted at registry 
`hub.docker.com/_/mongo`
the `_` is the verified original author; otherwise it often to be the author name

# `8` note when creating docker image
`COPY src dest` command vs `RUN cp src dest` 

COPY    src                 dest 
        # host-OS path      # container path

RUN cp  src                 dest
        # container path    # container path


# `9` what to share: dockerfile or docker image?
if sharing dockerfile, you will need the source code folder --> feasible within internal engineer team where all can access source code
with shared docker image, any one can run the app - this required the published image

# `10` how to publish a docker image?
use hub.docker.com will make your docker image public
how to have the image private ie available to authorized users only? `ref. Tuan` use gitlab 

#TODO how to view docker image layer?
