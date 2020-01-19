# docker-networking

## Creating user-defined bridge network
When you start Docker, a default `bridge` network is created automatically. All newly-started containers will connect automatically to it. However, you can also create user-defined custom bridge networks.

To create a new custom bridge network, simply run:

```
$ docker network create my_net
```
or

```
$ docker network create --driver bridge my_net
```

Both commands will do the same work. If you will not specify the driver name, it will create in the default network driver i.e. `bridge`.

Create user define bridge network

On user-defined networks like `my_net`, containers can not only communicate by IP address, **but can also resolve a container name to an IP address. This capability is called automatic service discovery.**

To ensure if the containers can communicate with each other, let us run three alpine containers namely A1, A2 and A3 on `my_net` network which we created earlier.

```
$ docker run --rm -it -d --name A1 --network my_net alpine /bin/sh
$ docker run --rm -it -d --name A2 --network my_net alpine /bin/sh
$ docker run --rm -it -d --name A3 --network my_net alpine /bin/sh
```
Now try to attach to any one of the containers and ping the other two using container name.

```
$ docker attach A1
    
    / # ping -c 2 A2
    PING A2 (172.19.0.3): 56 data bytes
    64 bytes from 172.19.0.3: seq=0 ttl=64 time=0.064 ms
    64 bytes from 172.19.0.3: seq=1 ttl=64 time=0.091 ms

    --- A2 ping statistics ---
    2 packets transmitted, 2 packets received, 0% packet loss
    round-trip min/avg/max = 0.064/0.077/0.091 ms

    / # ping -c 2 A3
    PING A3 (172.19.0.4): 56 data bytes
    64 bytes from 172.19.0.4: seq=0 ttl=64 time=0.071 ms
    64 bytes from 172.19.0.4: seq=1 ttl=64 time=0.132 ms

    --- A3 ping statistics ---
    2 packets transmitted, 2 packets received, 0% packet loss
    round-trip min/avg/max = 0.071/0.101/0.132 ms
```

To detach from the container without stop it, press CTRL+P CTRL+Q CTRL+Z

## Clean up

```
$ docker stop A1 A2 A3
$ docker network rm my_net
```
