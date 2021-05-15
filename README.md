# LogCabin Pressure Test|Yuxuan Jiang

To ensure no memory contention will happen

![image-20210515093746316](C:\Users\13982\AppData\Roaming\Typora\typora-user-images\image-20210515093746316.png)

To ensure no cpu contention will happen, three cores were left unused

![image-20210515093950475](C:\Users\13982\AppData\Roaming\Typora\typora-user-images\image-20210515093950475.png)During the test, No other software application will be used in order to ensure the testing environment's invariance during 

System Power was plugged in.



After each round of test, the Ubuntu20.04 WSL1 Instance (LogCabin nodes cannot communicate in WSL2 somehow) will be  

```bash
# Bootstrap the first server
locabind --config=locabin-1.conf --bootstrap
# Starting up the three nodes
locabind --config=locabin-1.conf --daemon --log=storage/server1.log
locabind --config=locabin-2.conf --daemon --log=storage/server2.log
locabind --config=locabin-3.conf --daemon --log=storage/server3.log
# Adding Servers: Reconfigure
export CLUSTER=$SERVER1IP,$SERVER2IP,$SERVER3IP
locabin-reconfigure --cluster=$CLUSTER set $SERVER1IP $SERVER2IP $SERVER3IP

ALLSERVERS=127.0.0.1:5254,127.0.0.1:5255,127.0.0.1:5256
build/Examples/Reconfigure --cluster=$ALLSERVERS set 127.0.0.1:5254 127.0.0.1:5255 127.0.0.1:5256

```



```bash
# Inspect or modify the state of a single LogCabin server
locabinctl

# Command-line client: Run various operations on a LogCabin Replicated Machine
locabin -h

# Run Benchmark (Plug in the power set and close any other programs)
ALLSERVERS=127.0.0.1:5254,127.0.0.1:5255,127.0.0.1:5256
build/Examples/Benchmark --cluster=$ALLSERVERS 

```

### Test Plan

Run Benchmark ten times, record the time taken to 



> Network Bandwidth Limit



> Network Latency

Among all components the above two should be the easiest to implement. But I didn't expect WSL to be without a support for netem. Because of that I was unable to add a network latency on my laptop.

<img src="C:\Users\13982\AppData\Roaming\Typora\typora-user-images\image-20210515180605709.png" alt="image-20210515180605709" style="zoom:25%;" />

One Bug Found:

After I force stopped the leader, it stayed in candidate state non-stop. And the term number kept incrementing.









#### Metrics

The time needed to write 10000 objects to the 3-nodes system. The object write operation was handled by the Benchmark program  provided by LogCabin.





CPU Limit:

On leader's cpu limit will increase its average response time. Thus causing two types of elections:

1. Other servers cannot receive heartbeat signals from the leader, thus leading to a election
2. The client is redirected to the current leader. Then connection succeeded.
3. The leader was overwhelmed by the request, thus causing a overtime response.
4. Causing an redirection and election and reconfiguration

![image-20210515182757646](C:\Users\13982\AppData\Roaming\Typora\typora-user-images\image-20210515182757646.png)

Follower Cpu Limit 50%

