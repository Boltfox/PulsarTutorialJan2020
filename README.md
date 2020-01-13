## Hello World

```
docker pull hello-world
docker run hello-world
```

## Docker pull

Pull an existing container from dockerhub
```
docker pull <image name:tag>
```
*(tag is optional, otherwise pulls latest)*

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

### Basic usage

Opening a terminal in image
```
docker run --rm -ti <image name> bash
```
Exit by pressing **[ctrl]+[d]** or writing **exit** in the terminal.

### Mounting volumes

You can mount directories with the **-v** tag, e.g.
```
docker run --rm -ti -v /home/batman/work/non_detections/:<path within image>
```
The **path within image** can be whatever you like. However, it is often
convenient to use the directory structure as it is in the file system. 
That way your code will not freak out if it depends on the file system's 
directory structure. This can also help you realizing where you are working.
Long path string can be annoying though, so it's also a good idea to just
use simple, concise catchwords, e.g. **/work**, **/data**,...

Example
```
docker run --rm -ti -v /home/batman/work/non_detections/:/home/batman/work/non_detections
docker run --rm -ti -v /home/batman/work/non_detections/:/data
```


