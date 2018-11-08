# Flogo demo by Square IT Services - Services application

## Introduction

This repository is part of the [**Flogo Demo by Square IT Services**](https://github.com/square-it/flogo-demo).

* [This Flogo application](#description) manages the screen display part.
* [Another Flogo application](https://github.com/square-it/flogo-demo-iot) is used to manage the screen display part.

Currently, these two applications communicates very simply through a REST service.

![Flogo Demo architecture](https://github.com/square-it/flogo-demo/blob/master/FlogoDemo.png)

## Description

The application can be run on any system where Go program [can be compiled to](https://dave.cheney.net/2015/08/22/cross-compilation-with-go-1-5).

## Prerequisites

* a system with Docker installed

## Usage

### Preparation

1. run a Flogo Web UI
```
docker run --name flogo -it -d -p 3303:3303 -e FLOGO_NO_ENGINE_RECREATION=false flogo/flogo-docker:v0.5.6 eula-accept
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

The Flogo Web UI is available at ```http://localhost:3303```
