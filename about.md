# Fraxis
The core Infraxis application platform upon which tenanted, account driven realtime applications can be built

# Configuration
If using containers, the configuration files described below should be stored outside of the containers running Fraxis.

### xstorm.cfg
This is a JSON file that holds the bulk of the customer configurable options. It is separated into _tables_, below the different tables will be detailed:

#### TABLENAME: XSTORM-CFG
This is the old xstorm.ini but in JSON format, the configuration paramaters include:

| Parameter | Type | Description |
| ------ | ------ | ------ |
| institution | string | The name of the instituion this Fraxis instance is being used at (e.g. DEMO, CCC, GCS) |
| system_id | int | A two digit ID that is used to identify the system, this is used as the last two digits of all generated UNIQUEREFs and CLONEREFs. If a single digit is entered then it will be left padded with a 0 |
| diagnostic_flag | int | 0 or 1. Controls whether any diagnostics are generated at all. "0" indicates no messages will be generated. |
| diagnostic_level | int |The Diagnostic Logging level.<br/><br/>Controls which type of messages are generated. Setting a specific diagnostic level automatically includes all the preceding levels.<br/><br/>Following is a description of the diagnostic logging levels. Higher numbers generate more messages and will have a corresponding negative performance impact.<br/><br/>Level 0:  SOFTWARE ERROR:<br/>Only log critical errors indicating that abnormal behaviour was noticed which - specifically - is indicative of a software bug or limitation. Will, without doubt, require a code change to repair.<br/><br/>Level 1:  TECHNICAL ALARM:<br/>Cases where abnormal behaviour is noticed which requires attention and may be related to technical non-business configuration.<br/><br/>Level 2: PROCESSING ALARM:<br/>Cases where abnormal behaviour is noticed which requires attention and is business configuration related.<br/><br/>Level 3: DECISION:<br/>General information which indicates the least common side of a decision or branch has been taken.<br/><br/>Level 4: INFORMATION: General information which indicates either side of a decision has been taken or just normal procedural information.<br/><br/>Level 5: VERBOSE INFO:<br/>As above, but also includes general information which may be reported inside "for" loops (or similar) and therefore may be extremely verbose. |
| replication_producer | string | The producer to user for the FIREcluster replication unless. For now the default value is the only option that can be used. (Default: libXsFIREProducer.so,GetActiveMQReplicationProducer) |
| firecluster_memcache_host | string | The host that stores the K/V pairs used for FIREcluster, currently the store is etcd (e.g. `http://172.31.3.132:2379`) |
| firecluster_election_interval | int | The time, in seconds, that there must be between DB leader elections. This is to stop the DB leader bouncing when there's comms issues |

### fireproducer-activemq.ini
This file is currently required for the FIREproducer configuration. The only config that should be altered is the broker_url. This should be a comma separated list of the ActiveMQ brokers to use, e.g. `tcp://192.168.0.1:61616,tcp://192.168.0.2:61616`, the order is the order that they will be used if there is a failover case. Make sure that they are configured in priorty order for the host this is being run on:

#### Example
```
# ActiveMQ broker URL
broker_url=failover://(tcp://192.168.0.1:61616,tcp://192.168.0.2:61616)

# Messaging type, either queue or topic
messaging_type=topic

# Boolean flag to inidicate whether to use persistent topic.
use_persistent_topic=true

# queue/topic name to consume
service_name=paystorm.replication
```
