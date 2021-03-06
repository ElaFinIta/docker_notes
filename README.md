# Example project with node:


- pull an image for node, latest version:
```sh
docker pull node
```

- run a container from node image:
```sh
docker run node
```

This will exit because node itself doesnt do anything. Add `-
it` to enter the shell mode:

```sh
docker run -it node
```
You are running node from the container, with the node version you pulled.

To put a server.js into a container:
- make a `Dockerfile` in the project root, with no extension

```dockerfile
FROM node:17-alpine3.14

WORKDIR /app

COPY . .

CMD ["node", "server.js"]
```
Build:
```sh
docker build . --tag node-server
```

To see your images:
```sh
docker images
```

You will see the newly made image node-server

Run a container from image:
```sh
docker run --name node-server-app -p 5000:8080 -d node-server
```

--name my-app --> the name of the app/container

-p is for port --> external port is 5000 and internal is 8080 (?)

-d is for detached

node-server is the name of the image


curl localhost:8080 --> will give the message of backend, jeee!

If you make changes to the server, and want to rebuild:

```sh
docker build --tag node-server
```

Enter a container:
```sh
docker exec -it node-server-app bash
```




- See running containers:
```sh
docker ps
```

- stop a container
```sh
docker stop <container_id>
```
- kill a container
```sh
docker kill <container_id>
```

- Make a new docker container >>> create a **docker-compose.yaml** file (note: indentation matters):

replace **username** with your docker hub username

nodejs-group here is the name of the project or service that we are going to run

```yaml
version: '3'
services:
  nodejs-project: #docker run -it -v ${PWD}:/work -w /work -p 5003:5000 username/nodejs-project:2.0.0 /bin/sh
    container_name: nodejs-group
    image: username/nodejs-group:2.0.0
    build: ./nodejs-project
    working_dir: /work
    entrypoint: /bin/sh 
    stdin_open: true
    tty: true
    volumes:
      - ./nodejs/src/:/work
    ports:
      - 5003:5005
```

entrypoint: /bin/sh >>> where we entry the container

tty >>> flag that tells Docker to allocate a virtual terminal session within the container

Port 5003 is the local port for UI, while 5005 here is the port of the server
Or: The nodejs-project service uses an image that???s built from the **dockerfile** in the current directory. It then binds the container and the host machine to the exposed port, 5005. This example service uses port, 5003.

??

- To make a project in nodejs >>> search in hub.docker.com for the node image (always prefer official images) >>> pick a version to download package, we used 12.4.0-alpine (bear in mind possible M1 chip compatibility issues)
- Make a **dockerfile** with the line:
```sh
FROM node:12.4.0-alpine
```
This will tell Docker to build an image starting with the node 12.4.0 image

A **dockerfile** is a text document that contains all the commands a user could call on the command line to assemble an image


??

- Initialize a node project with:
```sh
npm init -y
```
- Install needed dependencies for the particular project, ex. express

```sh
npm install express
```

```sh
npm install
```
- Move package.json and package-lock.json into `src`
- In package.json chane "main" >>> "server.js" which we are going to create next and add a "start" script >>> "node server.js"
- Make a server.js file that will send an index.html file

```sh
'use strict';

const express = require('express');
const path = require('path');

// Constants
const PORT = 5005;
const HOST = '0.0.0.0';

// App
const app = express();
app.get('/', (req, res) => {
    res.sendFile(path.join(__dirname, '/index.html'));
});

app.listen(PORT, HOST);
console.log(`Running on http://${HOST}:${PORT}`);
```

- Make a simple **index.html** file and add as script source server.js:
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <script src="server.js"></script>
</head>
<body>
    <h1>HELLO THERE!</h1>
</body>
</html>
```

![Screenshot 2022-03-02 at 21 34 46](https://user-images.githubusercontent.com/88823568/156436122-f6d1f93f-32bf-4fc3-9f51-d67b3109dac6.png)


- From src build with:
```sh
docker-compose build nodejs-project
```

nodejs-project is the name of the project/service we gave in the yaml file

[To build everything: docker-compose build]

- To run all the containers at once:
```sh
docker-compose up
```
or: 

```sh
docker-compose up -d
```
-d, --detach               Detached mode: Run containers in the background, print new container names. Incompatible with --abort-on-container-exit.
If you started Compose with docker-compose up -d, stop your services once you???ve finished with them:
```sh
docker-compose stop
```

- Enter a container:
```sh
docker exec -it <service_name> sh 
```
 Our <service_name> here is nodejs-project so:
```sh
docker exec -it nodejs-project sh 
```

- do
```sh
npm start
```
![Screenshot 2022-03-02 at 21 29 16](https://user-images.githubusercontent.com/88823568/156435041-60d98e06-4a4d-4ba0-b866-83155a0d8bcd.png)

and see the html sent in on port **5003**

![Screenshot 2022-03-02 at 21 30 29](https://user-images.githubusercontent.com/88823568/156435259-f1261907-268c-4854-b0b0-166d47b49ba9.png)

- To push image on docker hub: go into the folder where your project is and do:
```sh
docker login
```
- Then push image:
```sh
docker push <image>
```

<image> is <username>/nodejs-project:2.0.0 where 2.0.0 is a descriptive tag

- See your image on docker hub
