# Installation for development environment

## Install docker for mac
Please see below.  
https://store.docker.com/editions/community/docker-ce-desktop-mac

## Prepare docker-compose.yml
below is example docker-compose.yml.

```
version: "2"

services:
  example_container:
    image: alpine
    command: 'watch -n3 cat /var/www/index.html'
    volumes:
      - ./app:/var/www
```

## Start docker container
```
docker-compose up -d
```

## Stop docker container
```
docker-compose down
```

## Optinal Setup
Default directory mount is too slow to write.  
use NFS Mount as a workaround.

### Setup NFS

#### 1. add to /etc/exports
```
"${share_folder}" localhost -alldirs -mapall=501:20
```
* `${share_folder}` is share folder path.
* `mapall=501:20` is logged user id and group id

#### 2. add to /etc/nfs.conf
```
nfs.server.mount.require_resv_port = 0
```

#### 3. reload NFS
```
sudo nfsd update
```

### docker-compose.yml for NFS mount
```
version: "2"

services:
  example-container:
    image: alpine
    command: 'watch -n3 cat /var/www/index.html'
    volumes:
      - nfs-example:/var/www

volumes:
  nfs-example:
    driver: local
    driver_opts:
      type: nfs
      o: addr=host.docker.internal,actimeo=1
      device: ":${PWD}/app"
```


## Reference
- https://qiita.com/kunit/items/36d9e5fa710ad26f8010
