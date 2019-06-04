# Docker basics

## Containers

The most fundamental part of `Docker` are *containers*. There's a lot to say about them, but let's just run one:

```
docker run hello-world
```

Easy, right? Let's take a look at that container

```
docker ps
```

🤔 not there... Let's add the `-a` flag

```
docker ps -a
```

😀 there you are! We can also look a lot closer to that container with

```
docker inspect <container-id>
```

Now let's get serious. Let's run a full-fledged Ubuntu Linux:

```
docker run ubuntu:18.04
```

(notice how we specified a version; more on that to come)

🤔 it looks like it downloaded something, but not sure what...

```
docker ps -a
```

So it stops after it runs? Let's try something else:

```
docker run -it ubuntu:18.04
```

Cool, we're inside the container! `-it` specifies you want to go into the interactive mode (TBH, `i` is interactive and `t` is for docker to allocate a pseudo TTY interface for the interaction)

After toying around just `exit`. Let's give it another look:

```
docker ps -a
```

So the way containers work is that there is one single main process that gets assigned `pid 1`, which runs as the containers starts, and as soon as that process exits, the container is stopped, even if there were other processes running inside of it.

You may also have noticed that the first time you ran `docker run ubuntu:18.04` it took a while, but the second time it was immediate. What really happened is that docker tried to run a container based on the `ubuntu:18.04` image, and since it didn't have it locally, it pulled it from the public repository. Which brings us to:

## Images

_Images_ are the templates docker uses to create containers from. If you're familiar with [Object Oriented Programming](https://en.wikipedia.org/wiki/Object-oriented_programming) you may (sort of) think of images as _classes_ and containers as _instances_.

Check which images you have in your local repository by doing:

```
docker images
```

Let's try and get another image

```
docker pull mongo
```
This last command _pulled_ an image named `mongo` from [the public docker repository](https://hub.docker.com) to your local repository. This is very similar to what you achieve with `git pull` from a public `git` repository.

Cool! So now we have an image we didn't create a container from.

That's a wrap for the basics. Let's [move on to the next section](https://github.com/gvilarino/docker-workshop/tree/master/1-running-containers).
