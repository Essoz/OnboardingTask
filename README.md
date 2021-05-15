# Onboarding Task | Yuxuan Jiang

#### Introduction

> This is the onboarding task for *The next 7000 Replicated State Machines*, done by Yuxuan Jiang, a ZJU-UIUC sophomore.
>
> In this repository, the specific Raft Implementation, i.e. LogCabin, and the related programs to make it work are provided.



#### Testing Environment

> Software:
>
> - Windows10 Home
>
> - Ubuntu-20.04 Windows Subsystem for Linux 1
>
> - Python 2.7.16
> - Protobuf 2.6.1
> - Crypto++ 5.6.5
> - CPUlimit
>
> ----------------------------
>
> Hardware:
>
> - AMD Ryzen 7 4800H with Radeon Graphics 2.90 GHz
> - 16GB RAM

#### 3-Node System Setup

Please first install related software using instructions from 

- [LogCabin]: https://github.com/logcabin/logcabin

After installing LogCabin, you should be able to set up a three node system with command line tools it provided.

```bash
cd LogCabin
clear
# Bootstrap the first server
build/LogCabin --config=locabin-1.conf --bootstrap
# Starting up the three nodes
build/LogCabin --config=locabin-1.conf
build/LogCabin --config=locabin-2.conf ## run this in another terminal
build/LogCabin --config=locabin-3.conf ## run this in another terminal
# Adding Servers: Reconfigure ## run this in another terminal
ALLSERVERS=127.0.0.1:5254,127.0.0.1:5255,127.0.0.1:5256
build/Examples/Reconfigure --cluster=$ALLSERVERS set 127.0.0.1:5254 127.0.0.1:5255 127.0.0.1:5256
```

After running the above commands, you should have a three-node system running on your system.

#### Test Plan

I intend to testing the effect of slowing one component of a specific node among 5 components: 

1. Network Latency
2. Network Stability
3. CPU Usage Limit
4. Memory Usage Limit
5. Disk I/O Rate Limit

Sadly, since there is no netem support in WSL1/2 currently, I cannot use ```tc``` to set up network latency and related settings in my laptop. Thus these two tests will be done later. Till now only the third test **CPU Usage Limit** has been done.

##### Metric

I used the build-in Benchmark program as the metric for the performance of the system. Each call of Benchmark will write 1000 objects into the system and return the time consumed.

```bash
ALLSERVERS=127.0.0.1:5254,127.0.0.1:5255,127.0.0.1:5256
build/Examples/Benchmark --cluster=$ALLSERVERS
```



#### CPU usage pressure Test

```bash
# Use $top to find pid of three nodes
top
# Use $logcabinctl to find pid of the leader
logcabinctl info get
# Use $CPUlimit to limit cpu usage of a specific node
sudo cpulimit --pid $pid --limit 50
```

Here's the result.

![avatar](https://imgur.com/bwiB68X)

![avatar](https://imgur.com/wbgF5tK)

##### Result Interpretation

