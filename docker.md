### docker
```

docker ps -a
docker images
docker run -p 80:80 nginx         # run container (-p => publish)
docker run -rm -p 80:80 nginx     # run container and remove it when exits
docker run -d -p 80:80 nginx      # run container as deamon
docker run -d -p 80:80 nginx --name my_web    # run named container
docker run -it nginx sh           # interactive tty(shell)
docker exec -it adminer_1 sh      # interactive tty(shell) for existing containers

docker rm 305297d7a235 ff0a5c3750b9         # remove container
docker top 8a3ab5e82bef           # show top processes
docker inspect 8a3ab5e82bef    
docker prune                      # clean garbage containers
docker system prune                         # clean all garbage

docker build -t my_web .                             # build container from .Dockerfile
docker build -f my_web.Dockerfile -t my_web .        # build container from special file

docker network ls
docker network inspect

```
