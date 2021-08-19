# Localmongo

Local mongo replicaset running mongo:4.2-bionic/4.4-bionic for development using docker-compose.

## Setup

Switch to the directory of the version you want, and run:

```
docker-compose up -d
```

This should bring up 3 containers of mongodb, running a replicaset called `rs0`.

If you wish to switch versions, make sure you clean up first:

```
docker-compose stop
docker-compose rm
```

## Application

In your main application, you need to adjust your docker-compose in order for your application to be able to connect to the mongo containers. Use the below template of docker-compose.yml:

### For 4.2-bionic

```
version: "3"
services:
  app:
    build: .
    networks:
      - default
      - localmongo
    external_links:
      - localmongo1:mongo1
      - localmongo2:mongo2
      - localmongo3:mongo3
networks:
  localmongo:
    external:
      name: 42_default
```
### For 4.4-bionic

```
version: "3"
services:
  app:
    build: .
    networks:
      - default
      - localmongo
    external_links:
      - localmongo1:mongo1
      - localmongo2:mongo2
      - localmongo3:mongo3
networks:
  localmongo:
    external:
      name: 44_default
```
## Troubleshooting

Assuming you run `docker-compose up` inside the 4.4 folder, docker will create a network called `44_default`. You can view this by running `docker network ls`, and also by inspecting `docker inspect localmongo1`.

In the application's docker-compose.yml, we are specifying that the app must join 2 networks: the `default` one, as well as `localmongo` network, which is declared at the end of the file, pointing to an external network `localmongo_default`.

To troubleshoot connectivity issues, do `docker exec -it <appContainerID> bash` into your application container and look at ` cat /etc/hosts`. You should see 2 entries for the container, one per network. You should be able to do `ping mongo1`.

If it does not work, do `docker inspect <appContainerID>` and proceed from there. To clean up networks, use with care: `docker network prune`

Checkout the [documentation](https://docs.docker.com/compose/networking/) of docker-compose networking.

## Credits

[Kira Layto](https://stackoverflow.com/users/6008637/kira-layto) for their Stack Overflow [code snippet](https://stackoverflow.com/q/42190267/3461549).
