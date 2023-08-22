## 1. Choice of Base Image
`node:16-alpine3.16` image is used as the base image to build the containers. It is based on the Alpine Linux distribution and has lightweight and has a smaller footprint and also includes Node.js version 16, which is higher than the version that the application requires.

## 2. Directives
### _Client_
Multi-stage builds were used to implement the client docker file. This is essential because it allows for: - Smaller and more effective images by separating build from runtime dependencies.

##### Build Stage
`WORKDIR` directive sets the working directory for the container to /client.

`COPY` directive copies the package.json and package-lock.json files to the /client directory which are required to install the necessary dependencies for the application.

The `RUN` directive is used to execute the following commands; 
- `npm install --only=production` to install only the production dependencies
- `npm cache clean --force` clears the npm cache
- `rm -rf /tmp/*` respectively removes any temporary files.

The `COPY` directive is the used to copy the rest of the application code to the container's working directory.

The `RUN` directive builds the application by running `npm run build` and `npm prune --production` to remove development dependencies.

#####   Production Stage
The `WORKDIR` directive sets the working directory for the container to /client. 
`COPY` directive copies only the necessary files from the build stage to the production stage i.e. `build`, `public` ,`src` directories, and `package*.json` files.

The `ENV` directive sets the NODE_ENV environment variable inside the Docker image to production.

The `EXPOSE` directive the port 3000 to be exposed by the container when it is running.

The `RUN` directive excutes the following commands;
- `npm install --only=production` to install only the production dependencies
- `npm cache clean --force` clears the npm cache
- `rm -rf /tmp/*` respectively removes any temporary files.

`CMD` directive specifies the default command to run when a container is started.

### _Backend_
`WORKDIR` directive sets the working directory for the container to /backend.

`COPY` directive copies the package.json and package-lock.json files to the /backend directory which are required to install the necessary dependencies.

The `RUN` directive is used to execute the following commands;
- `npm prune --production` to remove development dependencies
- `npm cache clean --force` clears the npm cache
- `rm -rf /tmp/*` respectively removes any temporary files.

The `COPY` directive is the used to copy the rest of the application code to the container's working directory.

The `ENV` directive sets the NODE_ENV environment variable inside the Docker image to production.

The `EXPOSE` directive the port 5000 to be exposed by the container when it is running.

The `RUN` directive excutes the following commands;
- `npm prune --production` to remove development dependencies
- `npm cache clean --force` clears the npm cache
- `rm -rf /tmp/*` respectively removes any temporary files.

`CMD` directive specifies the default command to run when a container is started.

## 3. Networking
The docker-compose file defines: `backend-net` and `frontend-net`.
`ipam` is used to configure the IP addressing for the containers in each network.
The `driver: default` is used to set the default IPAM driver set.
The `frontend-net` network, uses the subnet `172.51.0.0/16` and IP of `172.51.0.101` 
`backend-net`  network uses the subnet `172.100.0.0/16` and IP of `172.100.0.100` on The `backend-net`, and `172.51.0.100` on the `frontend-net` network.
The database container is given the IP address `172.100.0.101` on the backend-net network.

Ports `3000` of the client container, `5000` of the backend container and `27017` the database container, are mapped to the host machine's ports `3000`, `5000` and `27017`.

## 4. Volume
A backend_voulme is defined. The volume is mounted to the backend container /data/db directory using the syntax backend_volume:/data/db in the database service persist data written

## 5. Git workflow
Along with other project files, Dockerfile were version-controlled using Git, and updates are committed and published to a Git repository.
