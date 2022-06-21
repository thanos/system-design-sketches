# system design sketches
a collection of notes and code sketches on system design


## Generic Scaling

### Wed services 

#### Load Balance 
Use a load balances between the users and the webworker. Could use Caddy or NGINX but __haproxy__ is always good.
#### Stateless Web Tier
Then keep state in the service DB and the users credit and edge state in __JWT__.

ISSUE: SPOF

### Database

Try and use the Jackal and Hide routine:
Master RDB for write, handling transactional and normalized inserts ( snow flake or star schemas) - GPU Db are interesting for speed
Use slaves (NoSQL) with  demoralized tables for reading.
These are easier to scale and partition.
Tier and rack the service
Understand what you need on the heap.

### Caching Web side
Difficult to get right
Content delivery network (__CDN__) all static entities and cache all pseudo static pages - i.e edge cache
  * watch cost
  * When to invalidate
  * back up (SPOF) ??
Use edge caches for business needs - redis, memsql
USE __CRTDs__ for maintaining consistency
Use caching to protect the db - ie against UI data intensive requests such as pivoting on end of day.  For instance reporting and analytics
consider special read column  db such as __clickhouse__.

Use a cache tier or better use an __orm__ that manages caching 

ISSUES: TTL, Consistency, SPOF?

### Static sites 
For informational websites or in front of the paywall consider  generating static site

