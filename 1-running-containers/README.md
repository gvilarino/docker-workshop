# Containers

## Running, detaching and attaching to containers

Let's run a mongo database! And with a cute name.

```
docker run --name db mongo
```

🤔 but I don't want to be attached to the output... Just CTRL+c to quit and let's remove that container.

```
docker rm db
```

Now let's run a new mongo container, but in the background with the `-d` flag (`d` as in `detach`).

```
docker run --name db -d mongo
```

Cool! Now let's check out the mongo database. First you need to sort-of-`ssh` into the container. You don't actually use `ssh`, instead you can _execute_ a command with the interactive mode, like so:

```
docker exec -it db mongo
```

You may understand this command as: "Hey, `docker`, please `exec`ute  the `mongo` command in the container named `db`, and do it as an ineteractive `-it` command, so I can attach my STDIN and STDOUT to it".

Now you're running the `mongo` command in the `db` container. Toy around and then CTRL+c to quit.

If you now do `docker ps` you'll notice the `db` container is still running. It didn't stop because the main process, the `mongo` database process (with `pid 1`), is still running. The process you killed by quitting was just the mongo shell.

Now let's connect to it from _another_ container.

```
docker pull gvilarino/docker-testing
```

This will get you a very simple `node.js` app that tries to connect to a mongo DB and informs if it succeeds. Once it downloads, just:

```
docker run -d --name app gvilarino/docker-testing
```

Now check if it did something:

```
docker logs app
```

It seems the app is listening on port 3000. Let's browse to `localhost:3000`

🤔 it doesn't reach the app... Let's look closer:

```
docker ps -a
```

As you can see under `PORTS`, it seems the app *is* listening in port 3000, but... 😮 Of course! That's just the container's _internal_ port! You need to *bind* the container port to a port in our host. So, kill this app container with `docker rm -f app` and let's create a new one properly.

```
docker run -d --name app -p 3000:3000 gvilarino/docker-testing
```

Now check `localhost:3000` again.

Ok, so we did manage to get there, but it seems it isn't reaching the DB. If you attach to the app container (with `docker exec`) and check the `index.js` file, you'll notice it's attempting to connect to a host named `db`. Our database container has that very same name. Then why isn't it reaching it?

## Docker networks

In order for a container to be visible to another one, they must both belong to the same _network_. Docker networks are virtual private network docker uses to connect containers safely and privately, as if they were real hosts in a real, physical network.

Let's create a simple network:

```
docker network create app-network
```

Now let's check it out:

```
docker network ls
```

It seems all's in place. Let's connect our containers to that network.

```
docker network connect app-network db
docker network connect app-network app
```

Now check `localhost:3000` one more time.

😎🐳

Ok, now you're ready to [learn how to work with images](https://github.com/gvilarino/docker-workshop/tree/master/2-building-images).
