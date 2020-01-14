# Create Overlay Network

## Step 1 - Initialise Swarm Mode
By default, Docker works as an isolated single-node. All containers are only deployed onto the engine. Swarm Mode turns it into a multi-host cluster-aware engine.

Task: Initialise Swarm Mode
To use the secrets functionality, Docker has to be in "Swarm Mode". This is enabled via docker swarm init

```
$ docker swarm init
```

Join Swarm Mode
Execute the command below on the second host to add it as a worker to the cluster.

```
$ docker swarm join ...
```

## Step 2 - Create Network
Overlay Networks are created using the Docker CLI, similar to creating a bridge network for connecting between hosts. When creating the network, a driver type of overlay is used. When new services are deployed via Swarm Mode, they can utilise this network allowing containers to communicate.

Task
To create the Overlay Network, use the CLI and define the driver. Networks can only be created via a Swarm Manager node. The network name would be app1-network.

```
$ docker network create -d overlay app1-network
```

All the networks can be viewed using:

```
$ docker network ls
```

Note: It's expected for the network not to appear on the worker nodes. The managers node handles network creation and services being deployed.

```
$ docker network ls
```

## Step 3 - Deploy Backend
Once the network has been created, services can be deployed and able to communicate with other containers on the network.

Task
The following will deploy a Redis service using the network. The name of the service will be redis that can be used for discovery via DNS.

```
$ docker service create --name redis --network app1-network redis:alpine
```

The next step will deploy a web app on a different node that will interact with Redis over the network.

## Step 4 - Deploy Frontend
With the overlay network and Redis deployed, it's now possible to deploy a Web App to use Redis to persist data. The application is configured to look up Redis via DNS. The app is configured to listen on port 3000, but the service will be exposed to the public on port 80.

Task
Create the new service will the command below:

```
$ docker service create \
    --network app1-network -p 80:3000 \
    --replicas 1 --name app1-web \
    felipecruz/node-server
```

With a two-node deployment, each container will be deployed onto different hosts.

```
$ docker ps
```

They'll use the overlay network and DNS discovery to communicate.

Test
Sending a HTTP request will persist the IP of the client in Redis.

```
$ curl localhost
```

As the service has been configured and deployed using Swarm Mode, it will take advantage of the load balancing.

```
curl host01
```
