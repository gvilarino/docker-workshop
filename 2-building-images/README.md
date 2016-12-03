# Images

## Building images

A docker images is composed by a number of layers. Each layer is built on top of the previous one and they're all immutable. You can't modify an existing layer, but you can build a new one that changes stuff in a previous layer. This is very similar to how `git`'s diff works.

In order to build an image, you will need a `Dockerfile`. Try the one for the web app image you've been using by `git clone`ing the `gvilarino/docker-testing` public github repo.

In that project's root dir, do:

```
docker build -t test-image .
```

Aaaaaand that's that 🐳

If you run it and inspect its contents, you'll notice it's the same as `gvilarino/docke-testing`'s, which mirrors the file system in that project.

Each instruction in the Dockerfile generates a single, immutable image layer.

## Understanding layers and leveraging the cache

Notice that if you run the same command again, it will take no time at all. This is because docker caches each layer and don't re-build them if the context and layer creation command doesn't change.

Now change the `FROM node:argon` line to `FROM node:6` so you try how it works with the latest `node.js` version and build it again

You'll see that all layers get rebuilt! This is because you changed the _base layer_.

Now change the string message in `index.js` and build again.

We now see that the base layer gets reused, but everything else gets re-done, including installing dependencies. In your everyday development workflow, you don't want to reinstall all dependencies just because you changed a single source file, you would only want that if you changed the `package.json` file. So let's tweak the `Dockerfile` to leverage the layer cache:

Change the `COPY [".", "/usr/src/"]` line to `COPY ["package.json", "/usr/src/"]`

And add a new `COPY [".", "/usr/src/"]` after the `RUN npm install` instruction.

Now rebuild (it will rebuild all the first time), then change the text again in `index.js`, and rebuild again.

Faster, right? 😎

You may publish this image by using the command `docker push`, but you'll need an account in [`hub.docker.com`](https://hub.docker.com). But you can do that later. Now let's focus on `docker-compose`



