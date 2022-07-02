# README

Demonstrate how to use `mitmproxy`  

TODO:

* mitmproxy Commands
* transparent and tls proxy

## 📋 Install

```sh
brew install mitmproxy

# config directory
ls ~/.mitmproxy
```

## 🧪 Quick test

```sh
# terminal
mitmproxy

# in another shell
curl --proxy localhost:8080 http://mitm.it
```

```sh
# terminal
mitmweb

# in another shell
open http://127.0.0.1:8081/
curl --proxy localhost:8080 http://mitm.it
```

## 🏠 Start

```sh
# start 
docker compose up -d

mitmweb --listen-host 0.0.0.0 --listen-port 40000

# open web portal
open http://127.0.0.1:8081/

curl -vvv --proxy localhost:40000 -i http://0.0.0.0:9001  
curl -vvv --proxy localhost:40000 -i http://0.0.0.0:9001/metrics  

curl -vvv --proxy localhost:40000 -i http://0.0.0.0:9001/panic

# post data
curl -vvv -X POST -d '{"user":"bob","pass":"123"}' --proxy localhost:40000 -i http://0.0.0.0:9001/echo
```

Set the intercept and repeat request.  It's possible to modify the payload like in fiddler.  

```txt
~u /echo & ~q
```

## Websockets

```sh
brew install websocat

mitmweb --listen-host 0.0.0.0 --listen-port 40000 --mode socks5

# send one message
echo "Look at testing websockets" | websocat --text ws://0.0.0.0:9001/ws/echo -n --socks5 0.0.0.0:40000  

# you can type and send info over websockets
websocat --text ws://0.0.0.0:9001/ws/echo -n --socks5 0.0.0.0:40000  

docker compose logs podinfo 
```

## Intercepting Proxy (docker compose)

Use with normal client proxy configuration.  

```sh
docker compose -f ./docker-compose.yaml -f ./docker-compose.mitmproxy.yaml up -d --build --force-recreate

docker compose logs mitmproxy 

open http://0.0.0.0:8081    

curl -vvv --proxy 0.0.0.0:8080 -i http://podinfo:9898

# cleanup
docker compose -f ./docker-compose.yaml -f ./docker-compose.mitmproxy.yaml down    
```

## Intercepting Reverse Proxy (docker compose)

Use with reverse proxy configuration.  

```sh
docker compose -f ./docker-compose.yaml -f ./docker-compose.mitmreverseproxy.yaml up -d --build --force-recreate

docker compose -f ./docker-compose.yaml -f ./docker-compose.mitmreverseproxy.yaml logs mitmproxy 

open http://0.0.0.0:8081    

# this will intercept and call
curl -vvv -i http://0.0.0.0:8080

# websockets work as well
websocat --text ws://0.0.0.0:8080/ws/echo -n 

# cleanup
docker compose -f ./docker-compose.yaml -f ./docker-compose.mitmreverseproxy.yaml down  
```

## 🧼 Clean up

```sh
docker compose down
```

## 👀 Resources

* mitmproxy is a free and open source interactive HTTPS proxy. [here](https://mitmproxy.org/)
* stefanprodan/podinfo tag [here](https://hub.docker.com/r/stefanprodan/podinfo/tags)
* stefanprodan/podinfo repo [here](https://github.com/stefanprodan/podinfo)  
* websocat repo [here](https://github.com/vi/websocat/blob/master/doc.md)  
* mitmproxy Modes of Operation [here](https://docs.mitmproxy.org/stable/concepts-modes/)  
