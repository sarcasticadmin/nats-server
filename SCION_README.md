## Building

Requirements: Go 1.19

Building nats:

```
go build -ldflags "-w" -o ./nats
```

## Setup

> Assuming that youve got SCiON setup (SCiONlab or otherwise) and nats built

Create the 1st server config, AS `18-ffaa:1:10bf`:

```
cat << EOF > server1.conf
server_name=s1
listen=4222
debug=false

scion {
  path_preference="bandwidth,latency"
}

cluster {
  name: c1
  listen: "nats://18-ffaa:1:10bf,127.0.0.1:4248"
}
```

Creating the 2nd server config, AS: `18-ffaa:1:10a`:
```
cat << EOF > server2.conf
server_name=s2
listen=5222
debug=true

scion {
  path_preference="bandwidth,latency"
}

cluster {
  name: c1
  listen: "nats://18-ffaa:1:10a0,127.0.0.1:5248"
  
  routes  = [
    "nats-route://[18-ffaa:1:10bf,127.0.0.1]:4248"
  ]
}
EOF
```
> Replace AS info for server 1 and server 2

Run server 1 serivce:

```
./nats -c server1.conf
```

Run server 2 service:

```
./nats -c server2.conf
```

You should see something along the lines for startup:

```
[5654] 2023/10/21 07:28:15.718928 [INF] Starting nats-server
[5654] 2023/10/21 07:28:15.719113 [INF]   Version:  2.9.22-RC.5
[5654] 2023/10/21 07:28:15.719256 [INF]   Git:      [not set]
[5654] 2023/10/21 07:28:15.719267 [DBG]   Go build: go1.19.12
[5654] 2023/10/21 07:28:15.719273 [INF]   Cluster:  c1
[5654] 2023/10/21 07:28:15.719278 [INF]   Name:     s2
[5654] 2023/10/21 07:28:15.719283 [INF]   ID:       ND5LI6MJ3G2IJKJW3EX3X7YLA2RN3VGXPYLTB5VHBEWWVMMAJVOEW34O
[5654] 2023/10/21 07:28:15.719299 [INF] Using configuration file: server2.conf
[5654] 2023/10/21 07:28:15.719476 [DBG] Created system account: "$SYS"
[5654] 2023/10/21 07:28:15.824822 [INF] Listening for client connections 18-ffaa:1:10a0,127.0.0.1:5222
[5654] 2023/10/21 07:28:15.824983 [DBG] Get non local IPs for "0.0.0.0"
[5654] 2023/10/21 07:28:15.825554 [DBG]   ip=10.0.2.15
[5654] 2023/10/21 07:28:15.825658 [DBG]   ip=10.4.0.143
[5654] 2023/10/21 07:28:15.825728 [INF] Server is ready
[5654] 2023/10/21 07:28:15.826167 [DBG] maxprocs: Leaving GOMAXPROCS=2: CPU quota undefined
[5654] 2023/10/21 07:28:15.826368 [INF] Cluster name is c1
[5654] 2023/10/21 07:28:15.870660 [INF] Listening for route connections 18-ffaa:1:10a0,127.0.0.1:5248
[5654] 2023/10/21 07:28:15.871077 [DBG] Trying to connect to route on [18-ffaa:1:10bf,127.0.0.1]:4248 (18-ffaa:1:10bf,127.0.0.1:4248)
path preference: bandwidth,latency
[5654] 2023/10/21 07:28:16.354100 [DBG] 18-ffaa:1:10bf,127.0.0.1:4248 - rid:4 - Route connect msg sent
[5654] 2023/10/21 07:28:16.354620 [INF] 18-ffaa:1:10bf,127.0.0.1:4248 - rid:4 - Route connection created
[5654] 2023/10/21 07:28:16.478098 [DBG] 18-ffaa:1:10bf,127.0.0.1:4248 - rid:4 - Registering remote route "ND6KHMHIGRA5ZFQZ6RH5EZCCMKTKNKUQM5I557MBGNJKYD2SPRGX72HH"
[5654] 2023/10/21 07:28:16.478142 [DBG] 18-ffaa:1:10bf,127.0.0.1:4248 - rid:4 - Sent local subscriptions to route
[5654] 2023/10/21 07:28:17.394507 [DBG] 18-ffaa:1:10bf,127.0.0.1:4248 - rid:4 - Router Ping Timer
```
> Note: Connections and Router Ping Timer (keepalive)
