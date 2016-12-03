# Docker swarm

Docker's swarm mode allows you to go all serious about large scale, highly available docker enviromnets. It basically lets you handle a cluster of machines as a single docker daemon, with automatic failover, container scheduling, routing and tons of goodies.

This last section will walk you through creating a simple swarm cluster and the basic concepts, but understaing docker swarm in its fullest is *way* beyond the scope of this workshop. But let's cut to the chase, shall we.

## Machine up

For a cluster, you'll need machines. Quickest, coolest way is by using `docker-machine`. It provides a series of tools for provisioning and managing docker-enabled VMs.

Start by creating the cluster master node:

```
docker-machine create -d virtualbox manager
```

That created a manager node. SSH into it:

```
docker-machine ssh manager
```

Cool, innit? Now `exit`

First we need to talk to that manager node from the outside, the quick way to do this is by doing:

```
eval $(docker-machine env manager)
```

That will set the env vars you need to talk to that manager's docker daemon from the outside (check `env | grep DOCKER` for more details). Note that every command you tell docker to run will actually run on the manager's docker daemon.

Now initialize the swarm:

```
docker swarm init --advertise-addr 192.168.99.100
// that IP address is the manager's default IP, change it if it's not your case.
```

Copy the `swarm join` command you get as the output to your clipboard. You'll need it soon.

Check the swarm daemon status by doing.

```
docker swarm info
```

You can see under `Swarm` the swarm state.

Now create some worker nodes:

```
docker-machine create -d virtualbox worker1
docker-machine create -d virtualbox worker2
```

See all machines with

```
docker-machine ls
```

Now let's make both worker nodes join the swarm cluster: `docker-machine ssh` into both workers and run the command in your clipboard. After doing so, do:

```
docker node ls
```

That's your cluster status. Pretty cool, huh?

Now, just like `docker-compose` works with the concept of services, so does docker swarm. Let's create a very simple service that pings docker.com

```
docker service create --name pinger --replicas 1 alpine ping docker.com
```

See some info by doing

```
docker service inspect --pretty pinger
```

Check its status by doing:

```
docker service ps pinger
```

You can see in which node it's running. Now let's get more replicas of that service (each replica is a container):

```
docker service scale pinger=5
```

Now if you `docker service ps pinger` you'll see in which nodes the new replicas are running.

Now you have a full-fledged local docker swarm.

For trying your previous `docker-compose` project in this swarm, go to the project root and do:

```
docker deploy --compose-file docker-compose.yml
```

Now you have you application running distributedly in a cluster of 3 VMs. How cool is that.

## Final words

These are just the docker basics, you'll learn (and suffer) a lot more when addressing real world scenarios. Hopefully this will encourage you to do some research on your own and spread the word.

Please feel free to update/fix anything that you see improvable in this guide.

Thanks for reading 🤓 🐳
