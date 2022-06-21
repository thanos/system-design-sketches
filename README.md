# system design sketches
a collection of notes and code sketches on system design


## Generic Scaling

### Horizontal v Vertical

#### Databases
Horizontal adds latency, cost, etc. Needs good partitioning and sharding strategies 
Vertical has hard limits (AWS RDS 24Tb) - unless virtual i.e cloud managed databases. Can leverage GPU Programable NICs and Disk Processors - by passing Kernel etc.  Think FPGAs etc.
 
#### CPU
For horizontal Use work queues or map/reduces
For vertical USE GPUs


### Wed services 

#### Load Balance 
Use a load balances between the users and the webworker. Could use Caddy or NGINX but __haproxy__ is always good. 
Use GeoDNS for load balancing to nearest data center.  __HA__ and __DR__


#### Stateless Web Tier
Then keep state in  NoSQL DB cluster and the users credit and edge state in __JWT__.

ISSUE: SPOF

### Database

Try and use the Jackal and Hide routine:
Master RDB for write, handling transactional and normalized inserts ( snow flake or star schemas) - GPU Db are interesting for speed
Use slaves (NoSQL) with  demoralized tables for reading.
These are easier to scale and partition.
Tier and rack the service
Understand what you need on the heap.

### Caching
Difficult to get right
Content delivery network (__CDN__) all static entities and cache all pseudo static pages - i.e edge cache
  * watch cost
  * When to invalidate
  * back up (SPOF) ?? - not really memcache, redis
Use edge caches for business needs - redis, memcache
USE __CRTDs__ for maintaining consistency
Use caching to protect the db - ie against UI data intensive requests such as pivoting on end of day.  For instance reporting and analytics
consider special read column  db such as __clickhouse__.
#### Strategies
 * Cache Aside - go to cache if miss then go to real source (db or calculator)
 * Read Through - The cache handles the miss
 * Write Through - The cache handles writes and then synchronously propagates to the db only returning
 * Write Around - Write to DB then if read add to cache
 * Write Back - The cache handles writes and then asynchronously propagates to the db only returning
Use a cache tier or better use an __orm__ that manages caching 

ISSUES: TTL, Consistency, SPOF?

#### Static sites 
For informational websites or in front of the paywall consider  generating static site

### Work Load Managment 

Use a work queue with scalable workers (
use spot instances when queue backs up
Use __broadway__ or __flow__ to handle back pressure and partitioning

### Queue Issues 
 * Latency
 * Staleness
 * Handling failure


###  Sharding & Partitioning
    Resharding and rebalancing is expensive
    Celebrity problem
    Joins are expensive so de-normalize




# Logging, Monitoring,  and Telemetry, Automation




## Estimations

### Sizes

|  bits |  size |  
|---|---|
| 10 | 1kb  | 
| 20 | 1 Mb | 
| 30 | 1 Gb |
| 40 | 1 Tb |
| 50 | 1 Pb |


### Latency

| unit | in seconds |
|--|--:|
| 1ns | 1/1,000,000,000s |
| 1us | 1/1,000,000s |
| 1ms | 1/1,000 |

| Op | Time | Relative Time |
|----|:------:|---------------:|
| L1 Read | 0.5ns | 1  |
| branch miss | 5ns | 10 |
| L2 Read | 7ns | 14 |
| Mutex op | 100ns | 200 |
| Ram op | 100ns | 200 |
| 1k zippy | 10kns 10us | 20,000 |
| 1k sent over 1Gbps | 10kns 10us | 20,000 |
| 1MB read from RAM | 250kns 250us | 500,000 |
| round trip same data center | 500kns 500us | 1,000,000 |
| Disk seek | 10m ns 10ms | 20,000,000 |
| 1MB from the net| 10m ns 10ms | 20,000,000 |
| 1MB from disk| 30m ns 30ms | 60,000,000 |
| packet CA to EU to CA| 150m ns 150ms | 300,000,000 |

see: https://colin-scott.github.io/personal_website/research/interactive_latency.html



## High Availability

Erlang - nine nines
Cloud three nines
Cloud load balancer 4 nines


| Availability | Down Time per Day | Down Time per Year |
|---|---|---|
| 99% | 14.4 minutes | 3.65 days |
| 99.9% | 1.4 minutes | 8.77 hours |
| 99.99% | 8.64 seconds | 52.6 minutes |
| 99.999% | 864 ms | 5.26 minutes |
| 99.9999% | 86 ms | 3.156 seconds |

### Twitter 

#### Assumptions
 * 300 million monthly active users
 * 50% use daily
 * 2 tweets per user per day
 * 10% contain media
 * store data for 5 years ?

