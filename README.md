SimpleScalar sim-outorder simulator with DIP policy cache
===

We implement the 
**Dynamic Insertion Policy based on Set Dueling** (DIP-SD) from the [ISCA'2007 paper][1]
in the _SimpleScalar_(http://www.simplescalar.com/) sim-outorder simulator.

- For the dedicated LRU sets and dedicated BIP sets, we dedicate 32 sets to each of these two fixed policies with the rest to be the followers. 
Hash functions in the implementation for all the cache configurations are:

 i. An LRU set iff ( cache\_setID mod ( total\_number\_of\_sets / 32 ) == 0 )

 ii. A BIP set iff ( (cache\_setID + 1) mod ( total\_number\_of\_sets / 32 ) == 0 )

 iii. The rest are the follower sets when both of the above conditions fail.

- We use a **BIPCTR** non-saturating counter for BIP. Whenever BIPCTR==0, we insert the line based on LRU policy, otherwise, LIP is used. (The width of BIPCTR can be set in the configuration file. It is set to 5-bit in the original paper.)

- We use a **PSEL** saturating counter for the selection of the final replacement policy between LRU and BIP for the follower sets. (The width of PSEL can be set in the configuration file. It is set to 10-bit in the original paper.) 

## Configuration ##

Format:

```
<name> <nsets>:<bsize>:<assoc>:<width_BIPCTR>:<width_PSEL>:<repl>
```

Cache replacement policy:
```
l: LRU;
d: DIP;
r: Random;
f: FIFO;
```

Example:

```
 # l2 data cache config, 256KB, 64-byte line, 8-way, DIP
-cache:dl2             ul2:512:64:8:5:10:d
```

Please further refer to a complete example configuration file _DIP-256K-8.baseline.cfg_.

## Compilation ##

```bash
make config-alpha
make clean
make sim-outorder
```


[1] Qureshi M K, Jaleel A, Patt Y N, et al. Adaptive insertion policies for high performance caching[C]//ACM SIGARCH Computer Architecture News. ACM, 2007, 35(2): 381-391.
