## Hello World

```
docker pull hello-world
docker run hello-world
```

## Docker pull

Pull an existing container from dockerhub
```
docker pull <image name>
```
Most of the software we use can be found at the MPIfR dockerhub page:

[https://hub.docker.com/u/mpifrpsr](https://hub.docker.com/u/mpifrpsr)

## Dockerfile

Build your own container from a Dockerfile.

cd into Dockerfile directory and run
```
docker build -t <name:tag> .
```
*(tag is optional)*

## Launching image




