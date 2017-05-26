# Docker compose

While docker allows you to easily build and link different apps with different environments and runtimes, doing so with more than just two or three containers quickly becomes tedious. `docker-compose` allows you to work with complex container systems very easily.

For working with `docker-compose`, you will need a `docker-compose.yml` file. Check out the one under the `gvilarino/docker-testing` repo.

First make sure you aren't running any containers. The from the project root, do:

```
docker-compose up -d
```

Magic ✨🐳

What just happened was that docker created all containers needed for the whole system, linked them appropriately in a network and bound all required ports.

```
docker-compose ps
```

You can see a simpler variation of `docker ps`, based on the items described in the `docker-compose.yml`. Each of these items are called _services_

Compare it to `docker ps`. They look alike, right? The difference is that containers names are generated as instances of the service, but the service itself is a compose-related concept.

Browse `localhost:3000` and you'll see a familiar page.

## Services

A service is the most elemental building block in a docker-compose environment. It's a specification on how you want containers for that service to run via `docker-compose`.

The `depends_on` property indicates which services should be started before starting the one with the property. This helps indicate service precedence.

You can also indicate specific environment variable values for containers with the `environment` property.

Change the value in `MONGO_URL` to `mongodb://foo/test`

Browse to `localhost:3000`. You'll see nothing changed... That's because you need to recreate the container!

```
docker-compose up -d
```

You'll see the `app` service gets re-created and if you once again browse to `localhost:3000` you'll se the error message.

## `docker-compose` as a developer's tool

Now let's say you want to use this setup to work, so you'll need a way for trying your code changes with compose. Let's start by building your own image *with* compose:

Remove the `image` property from the `app` service and add this one:

```
build: .
```

This instructs docker-compose where to look for a build context for that service. Now:

```
docker-compose build
```

You'll see some familiar output. After it's finished, do:

```
docker images
```

See how an image with a generated name is created. You can use this image as any other and run a container with `docker run`, but for now let's:

```
docker-compose up -d
```

The service got re-created based on the new image.

This allows us to leverage the layer cache for each re-build and you can `docker-compose build` for iterating our source... which sucks 😩

In order for your flow to be quick, you need to be able to re-create the service containers with new contents withouth re-building the whole image. So instead of re-building the image, you'll re-create the service container if there's a change in the source files. For that you have to _mount_ the local filesystem on top of the containers, like so:


```
volumes:
  - ./:/usr/src
  - /usr/src/node_modules
```

That's how you mount the source files into the container. We specifically exclude the `node_modules` path because we don't want to ever overwrite them even if theres a `node_modules` directory in the host machine.

Now change something in `index.js` and `docker-compose up -d`. You'll notice how the service container gets recreated with the new source without having to re-build the underlying image.

Now let's get serious, and [unleash the swarm](https://github.com/gvilarino/docker-workshop/tree/master/4-docker-swarm).
