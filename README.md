# HTTP-Routing-with-Traefik


This sample app accompanies the [Easy HTTP routing with Traefik](#) guide. The application is purposely kept simple to allow the focus to remain on the content of the guide - HTTP routing with Traefik. With Traefik, it is easy to use multiple services to split development tooling or provide additional visualization and troubleshooting tools.

Notice: This sample repo is intended to support the guide mentioned above. As such, the application code is purposely kept simple to keep the focus on the guide's content and should not be considered production ready.

# How the Application is working



![image](https://github.com/user-attachments/assets/7e5e8e57-d5b0-46c7-aba8-f45ce8901e3d)



### Docker Compose Services

This `docker-compose.yml` defines a multi-service application using Docker. Below is a breakdown of each service and its configuration:

#### 1. **proxy (Traefik Proxy)**

This service uses Traefik, a modern reverse proxy and load balancer, to route requests to other services.

```yaml
proxy:
  image: traefik:v3.1.2
  command: --providers.docker
  ports:
    - 80:80
  volumes:
    - /var/run/docker.sock:/var/run/docker.sock
```

- **image**: Specifies Traefik version `v3.1.2`.
- **command**: Enables Traefik to watch Docker for service configurations.
- **ports**: Exposes port `80` for HTTP traffic.
- **volumes**: Mounts the Docker socket to allow Traefik access to Docker events.



#### 2. **client**

This service builds the front-end client application and sets up hot reloading for development.

```yaml
client:
  build:
    context: ./client
    target: dev
  labels:
    traefik.http.routers.client.rule: "Host(`localhost`)"
    traefik.http.services.client.loadbalancer.server.port: 5173
  develop:
    watch:
      - path: ./client/src
        action: sync
        target: /usr/local/app/src
      - path: ./client/yarn.lock
        action: rebuild
```

- **build.context**: Specifies the build context as the `./client` directory.
- **target**: Uses a `dev` build target.
- **labels**: Configures Traefik to route traffic to the client on port `5173` using the host `localhost`.
- **develop.watch**: Watches the `./client/src` directory and `yarn.lock` file for changes. Synchronizes the source code and rebuilds when necessary.



#### 3. **api**

This service builds and runs the back-end API server with live synchronization for development.

```yaml
api:
  build:
    context: ./api
    target: dev
  environment:
    MONGO_URL: mongodb://mongo:27017
  labels:
    traefik.http.routers.api.rule: "Host(`localhost`) && PathPrefix(`/api`)"
    traefik.http.services.api.loadbalancer.server.port: 3000
  develop:
    watch:
      - path: ./api
        action: sync
        target: /usr/local/app/src
        ignore:
          - node_modules
          - yarn.lock
      - path: ./api/yarn.lock
        action: rebuild
```

- **build.context**: Uses the `./api` directory as the build context.
- **target**: Uses a `dev` build target.
- **environment**: Sets the `MONGO_URL` to connect to the `mongo` service.
- **labels**: Configures Traefik to route API requests matching `/api` on host `localhost` to port `3000`.
- **develop.watch**: Watches the `./api` directory for changes, excluding `node_modules` and `yarn.lock`. Synchronizes changes and triggers rebuilds when necessary.


#### 4. **mongo**

The MongoDB database service.

```yaml
mongo:
  image: mongo:7.0.14
  ports:
    - 27017:27017
  volumes:
    - ./dev/db:/docker-entrypoint-initdb.d
  environment:
    MONGO_INITDB_DATABASE: app
```

- **image**: Uses the MongoDB version `7.0.14`.
- **ports**: Maps port `27017` to the host for database access.
- **volumes**: Mounts `./dev/db` to initialize the database with custom scripts.
- **environment**: Sets the initial database name to `app`.


#### 5. **mongo-express**

A web-based interface for managing MongoDB databases.

```yaml
mongo-express:
  image: mongo-express:1.0.2
  labels:
    traefik.http.routers.db.rule: "Host(`db.localhost`)"
```

- **image**: Uses the `mongo-express` version `1.0.2`.
- **labels**: Configures Traefik to route requests to `db.localhost` for accessing the Mongo Express interface.




## Try it out

1. Clone the Repository using:   `git clone  https://github.com/JawadAhmed-Pro/HTTP-Routing-with-Traefik.git`
   You will see These files and verfiy that all the files are present.
![image](https://github.com/user-attachments/assets/1bfac9d8-6c0a-40a8-a75f-8ec2e79df513)

2. Run `docker compose up --build`
   ![Screenshot 2025-01-16 113458](https://github.com/user-attachments/assets/f106a4bc-354f-4078-bbc7-0aee874a07d6)

   
5. Open the site at http://localhost. Requests to get data will go to `/api/message`, which is routed to the API service.
   ![Screenshot 2025-01-16 113724](https://github.com/user-attachments/assets/11699a85-44f3-4a6e-90b4-2cebc01db890)

7. Open phpMyAdmin (database visualizer) at http://db.localhost
   

When you're done, run `docker compose down` to tear everything down.



## Contributing

Since this project is intended to support a specific use case guide, contributions are limited to bug fixes or security issues. If you have a question, feel free to open an issue!
