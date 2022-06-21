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


