# aries-acapy-cache-redis
ACA-Py Redis Base Cache Plugin
=======================================

ACA-Py uses a modular cache layer to story key-value pairs of data. The purpose
of this plugin is to allow ACA-Py to use Redis as the storage medium for it's
caching needs.

## Installation and Usage

### With Docker (Recommended)
Running the plugin with docker is simple and straight-forward. There is an
example [docker-compose.yml](./docker-compose.yml) file in the root of the
project that launches both ACA-Py and an accompanying Redis instance. Running
it is as simple as:

```sh
$ docker-compose up --build
```

If you are looking to integrate the plugin with your own projects, it is highly
recommended to take a look at both [docker-compose.yml](./docker-compose.yml)
and the [ACA-Py default.yml](./docker/default.yml) files to help kickstart your
project.

### Without Docker

First, install this plugin into your environment.

```sh
$ poetry install
$ poetry shell
```

Launch a local redis server for development.

```sh
$ docker run -d -v `pwd`/redis.conf:/usr/local/etc/redis/redis.conf \
  --name redis_cache -p 6379:6379 redis redis-server /usr/local/etc/redis/
```

When starting up ACA-Py, load the plugin along with any other startup
parameters. *Note: You may need to change the redis hostname*

```sh
$ aca-py start --arg-file ./docker/default.yml
```

For manual testing with a second ACA-Py instance, you can run the following.

```sh
$ aca-py start --arg-file ./docker/default.yml --admin 0.0.0.0 3003 \
  -it http 0.0.0.0 3002 -e http://localhost:3002 
```

### Configuration
Within the [default.yml](./docker/default.yml) file, all configuration for the
Redis Base Cache Plugin resides within the `plugin-config-value` block. The
configuration options are defined as follows:
 - `redis_cache.connection` The host connection URI for the Redis server. A
	 different DB can be selected by adding a `/0` or `/1` to the end of the URI
	 - The URI may start with `rediss://` for SSL connections and `redis://` for
		 non-SSL connections
 - `redis_cache.max_connections` The maximum number of connections to Redis
	 that the connection pool may allocate
 - `redis_cache.credentials.username` Username for the Redis server (if not
	 using the default user)
 - `redis_cache.credentials.password` The password for the Redis server/user
 - `redis_cache.ssl.cacerts` Path to the root CA information. Useful for when
	 using self-signed certificates.

## Running The Integration Tests

```sh
$ docker-compose -f int/docker-compose.yml build
$ docker-compose -f int/docker-compose.yml run tests
```
