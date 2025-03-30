# DockerAPIRateLimit

## Synopsis

DockerAPIRateLimit.py is a simple Prometheus exporter exposing docker API
rate limit current status.

It implements the official way to check the status described in
"View hourly pull rate and limit" of:

https://docs.docker.com/docker-hub/usage/pulls


## Description

DockerAPIRateLimit.py relies on the following standard Python3 modules only.

getopt, argparse, json, http

I believe there is no need to install additional modules.

Just place DockerAPIRateLimit.py somewhere, for example /usr/local/bin, and
run it as a service.

'docker-api-ratelimit.service' is an example of systemd service definition.

* `sudo cp docker-api-ratelimit.service /etc/systemd/system`
* `sudo systemctl enable docker-api-ratelimit`
* `sudo systemctl start docker-api-ratelimit`

To test, use curl like described in 'Output' below.

When you configure scpraping definition of Prometheus, 1800s would be
enough for scraping interval from my experiences.
        
### Command line options

By default DockerAPIRateLimit listens on tcp 29290. You can change listen
port and address using -p and -b options.

```
$ python3 DockerAPIRateLimit.py -h
usage: DockerAPIRateLimit.py [-h] [-b BIND_ADDRESS] [-p PORT] [--debug]

DockerAPIRateLimit.py

options:
  -h, --help            show this help message and exit
  -b BIND_ADDRESS, --bind_address BIND_ADDRESS
  -p PORT, --port PORT
  --debug
```

* -h
    * Show the above help message
* -b BIND_ADDRESS
    * bind address to listen. Default is '0.0.0.0'
* -p PORT
    * bind port to listen. Default is 29290
* --debug
    * Enable debug messages

### Output

The below example is from a running DockerAPIRateLimit.py instance.
Note that source IP addresses below are dummies. 

```
$ curl http://localhost:29290/metrics
# TYPE docker_api_ratelimit_limit gauge
# HELP docker_api_ratelimit_limit
docker_api_ratelimit_limit{source="192.168.0.1"} 100
# TYPE docker_api_ratelimit_remaining gauge
# HELP docker_api_ratelimit_remaining
docker_api_ratelimit_remaining{source="192.168.0.1"} 98
$
```

## TODO
* Embedding duration information ('w=21600' (6 hours) in the example below)
    in the REST API result could make sense.
```
(snip)
ratelimit-limit: 100;w=21600
ratelimit-remaining: 98;w=21600    
(snip)
```

## History
* 2020/03/15 Initial version
* 2025/03/31 Split repository from junkbox

## License
Apache License, Version 2.0
