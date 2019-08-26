# How to use
1. Clone the repository to your local machine

2. Make sure you have [Docker](https://docs.docker.com/install/) installed on your local machine

3. Install [Docker Compose](https://docs.docker.com/compose/install/)

4. Open a terminal, cd to this Docker-Task folder

5. Run 
```
sudo docker-compose up --build
```

6. If everything goes fine, your console will finally output -
```
docker-node-mongo | MongoDB Connected
```

7. Test the application by making a GET request to
```
localhost:1234/products/test 
```
&nbsp;  &nbsp;  &nbsp;  (by using [Postman](https://www.getpostman.com/) or just simply visiting the link on your browser). You should be see the following text -  
```
Greetings from the Test controller!
```
&nbsp;  &nbsp;  &nbsp;  verifying that our app was properly setup.

8. You can now make **GET, POST, PUT, DELETE** requests to this
running server using **Postman** and the procedure followed in [this article](https://codeburst.io/writing-a-crud-app-with-node-js-and-mongodb-e0827cbbdafb)

9. If you wish to run the MongoDB shell for the app's database, open up a new terminal window, cd to this Docker-Task folder,
and making sure that the container is running, type
```
mongo
```
&nbsp;  &nbsp;  &nbsp;  An interactive mongo shell should open up.

&nbsp;  &nbsp;  &nbsp;  (Alternatively, we could have added the *--detached* parameter while running our *docker-compose up* command, which would have caused our container to run in the background, letting us type 
&nbsp;  &nbsp;  &nbsp;  &nbsp;  commands in the same terminal window. So we could have run the *mongo* command without opening another terminal window)


## Quick Explanation
Since our app consists of two different components (the main Node.js app and our MongoDB database), we will need two different Docker containers for each of them. To make these two components work together like a single unit, we use create a Docker Compose file(**docker-compose.yml**). Compose is a tool for defining and running multi-container Docker applications. 

Using Compose is basically a three-step process:

1. Define your app’s environment with a Dockerfile so it can be reproduced anywhere.

2. Define the services that make up your app in docker-compose.yml so they can be run together in an isolated environment.

3. Run 
```
sudo docker-compose up --build
```
and Compose starts and runs our entire app.
We can verify that, in our case, two containers have been create by running
```
sudo docker container ls
```
which will display the details of currently running containers, that looks similar to this:
```
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                      NAMES
7c0e58ae932a        docker_app          "docker-entrypoint.s…"   15 seconds ago      Up 13 seconds       0.0.0.0:1234->3000/tcp     docker-node-mongo
540fe6382bf7        mongo               "docker-entrypoint.s…"   15 seconds ago      Up 14 seconds       0.0.0.0:27017->27017/tcp   mongo
```

## Data Persistence 
Note the very last configuration command written under our *mongo* service:
```
    volumes:
        - /data/db
```
This maps the data storage location of our container to the directory */data/db* (relative to the 
directory in which this *docker-compose.yml* file resides)
This ensures that whatever the data volume the container creates persists inside the */data/db* directory even
when the container has stopped running. So, the next time we start up the container, the same volume data will still be restored.

To see exactly which directory of the container was mapped to the */data/db* directory, run the command
```
sudo docker inspect mongo
```
Where *mongo* is the name of the container in which our *MongoDB* is running(as specified in the *container-name* attribute of the *mongo* service in our **docker-compose.yml**)

In the JSON-format output, look for a key named *"Mounts"*. You should find
```
{
    "Type": "volume",
    "Name": "0ec01aec488629f740132dc655ad8474c96980b1d0c8d2208aecb30bb2384598",
    "Source": "/var/lib/docker/volumes/0ec01aec488629f740132dc655ad8474c96980b1d0c8d2208aecb30bb2384598/_data",
    "Destination": "/data/db",
    ...
    ...
    ...
    ...
}
```

This tells you that when you mounted a volume (*/data/db*), it has created a folder /var/lib/… for you, which is where it puts all the files, etc that you would have created in that volume. 
