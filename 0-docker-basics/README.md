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
docker run ubuntu:14.04
```

(notice how we specified a version; more on that to come)

🤔 it looks like it downloaded something, but not sure what...

```
docker ps -a
```

So it stops after it runs? Let's try something else:

```
docker run -it ubuntu:14.04
```

Cool, we're inside the container! `-it` specifies you want to go into the interactive mode (TBH, `i` is interactive and `t` is for docker to allocate a pseudo TTY interface for the interaction)

After toying around just `exit`. Let's give it another look:

```
docker ps -a
```

So the way containers work is that there is one single main process that gets assigned `pid 1`, which runs as the containers starts, and as soon as that process exits, the container is stopped, even if there other processes running in it.

You may also have noticed that the first time you ran `docker run ubuntu:14.04` it took a while, but the second time it was immediate. What really happened is that docker tried to run a container based on the `ubuntu:14.04` image, and since it didn't have it locally, it pulled it from the public repository. Which brings us to:

## Images

_Images_ are the tempalates docker uses to create containers from. Check which ones we have

```
docker images
```

Let's try and get another image

```
docker pull mongo
```

Cool so now we have an image we didn't create a container from.

That's a wrap for the basics. Let's move on to the next stage.
