## 1. Building the image
Use the following command to build docker image:

```
docker build -t jupyter .
```

The `-t` flag specifies that we want to give the image a specific name (in our case jupyter). 

The `.` at the end of the command specifies the directory in which the *Dockerfile* lies. In our case this is our current working directory which we specify with a dot.

## 2. Starting the container
Use the following command to start the container:

```
docker run -it -p 8888:8888 -v "$(pwd)/project:/project" jupyter
```

The `-it` flag specifies to run the container in interactive mode (i.e. printing the output of the container to our shell). 

The `-p` flag specifies that we map port 8888 of the container (the one after the :) to port 8888 of our host machine (the one before the :). It effectively redirects our localhost:8888 to the localhost:8888 of the container which is where the notebook server is running.

The `-v` flag specifies that we want to share a folder with the container, meaning that files can be exchanged instantly and also persist on our host machine after the container is stopped. The part before the `:` specifies the folder *project* in our currend working directory (hence `$(pwd)`) as our endpoint of the folder sharing. The part after the `:` specifies the absolute path of the respective endpoint in the container. It is also the directory the container is already using (as specified in the Dockerfile) which means that all files in the project folder will be visible when starting jupyter notebook. 

The `jupyter` at the end is the name of the image we want to use.

## 3. Accessing the notebook
The container prints the jupyter kernel log into the terminal. Opening the link `http://127.0.0.1:8888/?token=...` in a browser opens the frontend of the jupyter server created by the container. You can now simply use jupyter notebooks as if it ran on your on machine (which it still does, technically, but the container does not know that and therefore we must act as if it didn't).

## 4. Storing and accessing files
This container does not store anything you did after starting the container after it was stopped. This is why we mirror the *project* folder into the container (specified by the `v` flag in the `docker run` command). Any changes made within that folder are instantly effective in the *project* folder within the container and vice versa. 

The container starts within this project folder by default. Therefore, any notebooks placed in our project folder also exist in the container. Any output stored in the container's *project* folder persists on our host system even after the container is stopped.

## 5. Stopping the Container
Simply press `CTRL+C` in the terminal displaying the jupyter notebook kernel and confirm with `y`. This should stop the container. If it does not, open another terminal, run `docker ps` to get a list of running containers and their respective names. Then run `docker stop <container_name>`. The container name is something like *stoic_hamilton* and is automatically assigned to a container when it is started. If that also does not work, run `docker kill <container_name>`. 

## 6. Opening container terminal
Executing `docker run` with this image will turn the terminal into the output of the jupyter kernel where you won't be able to enter futher commands. Should you want to enter the terminal of the running container, execute 
```
docker ps
```
to get a list of running containers and their respective names. Then execute the following command to get into the container:
```
docker exec -it <container_name> /bin/bash
```