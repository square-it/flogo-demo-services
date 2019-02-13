# Flogo demo by Square IT Services - Services application

## Introduction

This repository is part of the [**Flogo Demo by Square IT Services**](https://github.com/square-it/flogo-demo).

* [This Flogo application](#description) manages the interactions with end-users (which smiley to display?, the list of smileys, ...)
* [Another Flogo application](https://github.com/square-it/flogo-demo-iot) is used to manage the screen display part.

These two applications can communicate through a REST service or via an MQTT broker.

![Flogo Demo architecture](https://github.com/square-it/flogo-demo/blob/master/FlogoDemo.png)

## Description

This application has a flow triggered indistinctly by a [REST](https://github.com/TIBCOSoftware/flogo-contrib/tree/master/trigger/rest) or [MQTT](https://github.com/TIBCOSoftware/flogo-contrib/tree/master/trigger/mqtt) trigger which take as a parameter the Unicode identifier of [any emoji defined in the OpenMoji library](http://openmoji.org/library.html).

It can be [compiled and run](#usage) on any system where Go program [can be compiled to](https://dave.cheney.net/2015/08/22/cross-compilation-with-go-1-5).

As any flow-based Flogo application, it can be [modified with the Flogo Web UI](#development).

## Prerequisites

* [Go](https://golang.org/) >= 1.11
* [Dep](https://github.com/golang/dep)
* [Flogo CLI](https://github.com/TIBCOSoftware/flogo-cli) >= 0.5.8
* [Docker](https://www.docker.com/), for testing & development

## Usage

### Build from sources

1. clone this repository
```
git clone https://github.com/square-it/flogo-demo-services.git
cd flogo-demo-services
```

2. compile the application into a native executable
```
flogo ensure
flogo build -e
```

### Prepare

1. launch a Mosquitto MQTT broker (required):
```
docker run --name mosquitto -d -p 1883:1883 -p 9001:9001 eclipse-mosquitto
```

2. to enable OpenTracing, run a Zipkin collector (optional):
```
docker run --name zipkin -d -p 9411:9411 openzipkin/zipkin
```
> For detailed instructions read [OpenTracing collectors for Flogo](https://github.com/square-it/flogo-opentracing-listener#collectors).

### Test

> Considering this application will run on a host reachable at *192.168.1.1* with a Raspberry Pi reachable at *192.168.1.2*
 
1. define Raspberry Pi and MQTT configuration
```
export RPI_HOST=192.168.1.2
export RPI_PORT=4445

export MQTT_BROKER=192.168.1.1:1883
export MQTT_DEMO=demo
```

2. run the executable
```
cd bin
./flogo-demo-services
```

To enable OpenTracing with a Zipkin HTTP collector listening on 192.168.1.1:9411, run instead
```
export FLOGO_OPENTRACING_IMPLEMENTATION=zipkin
export FLOGO_OPENTRACING_TRANSPORT=http
export FLOGO_OPENTRACING_ENDPOINTS=http://192.168.1.1:9411/api/v1/spans
cd bin
./flogo-demo-services
```

3. test with a sample smiley

a. with the REST trigger
```
curl http://192.168.1.1:4445/v1/smiley/1F605
```

b. with a MQTT message
```
docker run -it --rm --name mqtt-publisher efrecon/mqtt-client pub -h 192.168.1.1 -t "demo" -m "1F609"
```

## Development

1. run a Flogo Web UI
```
docker run --name flogo -it -d -p 3303:3303 -e FLOGO_NO_ENGINE_RECREATION=false flogo/flogo-docker:v0.5.8 eula-accept
```

2. install custom-made activities contributions
```
docker exec -it flogo sh -c 'cd /tmp/flogo-web/build/server/local/engines/flogo-web && flogo install github.com/square-it/flogo-contrib-activities/command'
docker exec -it flogo sh -c 'cd /tmp/flogo-web/build/server/local/engines/flogo-web && flogo install github.com/square-it/flogo-contrib-activities/copyfile'
```

3. restart the Flogo Web UI container
```
docker restart flogo
```

The Flogo Web UI is available at ```http://localhost:3303```.

Import the application using the [provided *flogo.json*](./flogo.json).
