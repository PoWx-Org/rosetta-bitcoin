<p align="center">
  <a href="https://www.rosetta-api.org">
    <img width="90%" alt="Rosetta" src="https://www.rosetta-api.org/img/rosetta_header.png">
  </a>
</p>
<h3 align="center">
   OBTC Rosetta 
</h3>
<p align="center">
  <a href="https://circleci.com/gh/coinbase/rosetta-bitcoin/tree/master"><img src="https://circleci.com/gh/coinbase/rosetta-bitcoin/tree/master.svg?style=shield" /></a>
  <a href="https://coveralls.io/github/coinbase/rosetta-bitcoin"><img src="https://coveralls.io/repos/github/coinbase/rosetta-bitcoin/badge.svg" /></a>
  <a href="https://goreportcard.com/report/github.com/coinbase/rosetta-bitcoin"><img src="https://goreportcard.com/badge/github.com/coinbase/rosetta-bitcoin" /></a>
  <a href="https://github.com/coinbase/rosetta-bitcoin/blob/master/LICENSE.txt"><img src="https://img.shields.io/github/license/coinbase/rosetta-bitcoin.svg" /></a>
  <a href="https://pkg.go.dev/github.com/coinbase/rosetta-bitcoin?tab=overview"><img src="https://img.shields.io/badge/go.dev-reference-007d9c?logo=go&logoColor=white&style=shield" /></a>
</p>

<p align="center"><b>
OBTC-ROSETTA IS CONSIDERED <a href="https://en.wikipedia.org/wiki/Software_release_life_cycle#Alpha">ALPHA SOFTWARE</a>.
USE AT YOUR OWN RISK! PoWx ASSUMES NO RESPONSIBILITY NOR LIABILITY IF THERE IS A BUG IN THIS IMPLEMENTATION.
</b></p>

## Overview
`obtc-rosetta` provides a reference implementation of the Rosetta API for
Bitcoin in Golang. If you haven't heard of the Rosetta API, you can find more
information [here](https://rosetta-api.org).

`obtc-rosetta` is fully based on `rosetta-bitcoin` (https://github.com/coinbase/rosetta-bitcoin) - Rosetta API implementation for Bitcoin.

## Usage
As specified in the [Rosetta API Principles](https://www.rosetta-api.org/docs/automated_deployment.html),
all Rosetta implementations must be deployable via Docker and support running via either an
[`online` or `offline` mode](https://www.rosetta-api.org/docs/node_deployment.html#multiple-modes).

**YOU MUST INSTALL DOCKER FOR THE FOLLOWING INSTRUCTIONS TO WORK. YOU CAN DOWNLOAD
DOCKER [HERE](https://www.docker.com/get-started).**

### Install
Running the following commands will create a Docker image called `rosetta-bitcoin:latest`.

#### From Docker Hub
After installinf Docker, run:
```text
lorem ipsum
```

#### From Source
After cloning this repository, run:
```text
make build-local
```


### Run
Running the following commands will start a Docker container in
[detached mode](https://docs.docker.com/engine/reference/run/#detached--d) with
a data directory at `<working directory>/bitcoin-data` and the Rosetta API accessible
at port `8080`.

#### Mainnet:Online
```text
docker run -d --rm --ulimit "nofile=100000:100000" -v "$(pwd)/bitcoin-data:/data" -e "MODE=ONLINE" -e "NETWORK=MAINNET" -e "PORT=8080" -p 8080:8080 -p 9901:9901 obtc-rosetta
```
_If you cloned the repository, you can run `make run-mainnet-online`._

#### Mainnet:Offline
```text
docker run -d --rm -e "MODE=OFFLINE" -e "NETWORK=MAINNET" -e "PORT=8081" -p 8081:8081 rosetta-bitcoin:latest
```
_If you cloned the repository, you can run `make run-mainnet-offline`._

#### Testnet:Online
```text
docker run -d --rm --ulimit "nofile=100000:100000" -v "$(pwd)/bitcoin-data:/data" -e "MODE=ONLINE" -e "NETWORK=TESTNET" -e "PORT=8080" -p 8080:8080 -p 19898:19898 obtc-rosetta
```
_If you cloned the repository, you can run `make run-testnet-online`._

#### Testnet:Offline
```text
docker run -d --rm -e "MODE=OFFLINE" -e "NETWORK=TESTNET" -e "PORT=8081" -p 8081:8081 rosetta-bitcoin:latest
```
_If you cloned the repository, you can run `make run-testnet-offline`._

## System Requirements
`obtc-rosetta` has been tested on an [AWS c5.9xlarge instance](https://aws.amazon.com/ec2/instance-types/c5).
This instance type has 32 vCPU and 68 GB of RAM. Much less powerful can be used but perfomance is not guaranteed. 

### Network Settings
To increase the load `rosetta-bitcoin` can handle, it is recommended to tune your OS
settings to allow for more connections. On a linux-based OS, you can run the following
commands ([source](http://www.tweaked.io/guide/kernel)):
```text
sysctl -w net.ipv4.tcp_tw_reuse=1
sysctl -w net.core.rmem_max=16777216
sysctl -w net.core.wmem_max=16777216
sysctl -w net.ipv4.tcp_max_syn_backlog=10000
sysctl -w net.core.somaxconn=10000
sysctl -p (when done)
```
_We have not tested `rosetta-bitcoin` with `net.ipv4.tcp_tw_recycle` and do not recommend
enabling it._

You should also modify your open file settings to `100000`. This can be done on a linux-based OS
with the command: `ulimit -n 100000`.

### Memory-Mapped Files
`rosetta-bitcoin` uses [memory-mapped files](https://en.wikipedia.org/wiki/Memory-mapped_file) to
persist data in the `indexer`. As a result, you **must** run `rosetta-bitcoin` on a 64-bit
architecture (the virtual address space easily exceeds 100s of GBs).

If you receive a kernel OOM, you may need to increase the allocated size of swap space
on your OS. There is a great tutorial for how to do this on Linux [here](https://linuxize.com/post/create-a-linux-swap-file/).


## Testing with rosetta-cli
To validate `obtc-rosetta`, [install `rosetta-cli`](https://github.com/coinbase/rosetta-cli#install)
and run one of the following commands:
* `rosetta-cli check:data --configuration-file rosetta-cli-conf/testnet/config.json`
* `rosetta-cli check:construction --configuration-file rosetta-cli-conf/testnet/config.json`
* `rosetta-cli check:data --configuration-file rosetta-cli-conf/mainnet/config.json`
* `rosetta-cli check:construction --configuration-file rosetta-cli-conf/mainnet/config.json`

## Development
* `make deps` to install dependencies
* `make test` to run tests
* `make lint` to lint the source code
* `make salus` to check for security concerns
* `make build-local` to build a Docker image from the local context
* `make coverage-local` to generate a coverage report

## License
This project is available open source under the terms of the [Apache 2.0 License](https://opensource.org/licenses/Apache-2.0).


