

TWCS: Time Window Compaction Strategy
==================================


Motivation
----------

TWCS is a proposed improvement on DTCS. The motivations for TWCS are explained at:

https://issues.apache.org/jira/browse/CASSANDRA-9666

And:

http://www.slideshare.net/JeffJirsa1/cassandra-summit-2015-real-world-dtcs-for-operators

Version Compatibility
---------------------

We maintain our own [semantic versioning](http://semver.org/) for this project. This will make it easier in the long run. Compatibility is as follows:

4.0: Apache Cassandra 3.1
3.0: Apache Cassandra 2.2
2.0: Apache Cassandra 2.1
1.0: Apache Cassandra 2.0

Setup
-----

Checkout the repo:

```
git clone https://github.com/jeffjirsa/twcs/
```

Switch to the right version (for newest 2.1 release, for example):

```
git checkout origin cassandra-2.1 
```

Build the jar:

```
cd TimeWindowCompactionStrategy
mvn compile
mvn package
```

The resulting jar will be placed in target/, copy it to the classpath of your cassandra server.

Enabling
----------

Use the `WITH compaction=` directive to set the compaction strategy.  By default, TWCS creates 1 Day buckets and assumes MICROSECOND resolution.

Use `compaction_window_unit` of MINUTES, HOURS, or DAYS and `compaction_window_size` as an integer to group sstables into buckets of a fixed size that matches your use case.  
Use `timestamp_resolution` to let the compaction strategy know which time unit to use.

To create 3 minute buckets of microsecond resolution (for example, for data with very short TTL), you would use:

    CREATE TABLE twcs (
    id int,
    value int,
    PRIMARY KEY ((id))
    ) WITH compaction= {
        'compaction_window_unit': 'MINUTES',
        'compaction_window_size': '3',
        'class':'com.jeffjirsa.cassandra.db.compaction.TimeWindowCompactionStrategy'
        };

You should target fewer than 50 buckets per table based on your TTL. A TTL of 90 days may want 3 day buckets, where a TTL of 2 hours may want 5 minute buckets.
 

Source
------

This repository ( https://github.com/jeffjirsa/twcs ) will be used for standalone TWCS code (simple jar to drop into lib/). For practical purposes, in this repository the compaction strategy is `com.jeffjirsa.cassandra.db.compaction.TimeWindowCompactionStrategy`

TWCS is also available as a C* fork (in the hopes that patches are accepted upstream): 

2.1: https://github.com/jeffjirsa/cassandra/tree/twcs-2.1

2.2: https://github.com/jeffjirsa/cassandra/tree/twcs-2.2

Trunk / 3.0: https://github.com/jeffjirsa/cassandra/tree/twcs

In the full fork, the compaction strategy is: `org.apache.cassandra.db.compaction.TimeWindowCompactionStrategy`

License
-------

TWCS is provided under the Apache License, V2: http://www.apache.org/licenses/LICENSE-2.0
