**EC2**
1.) aws provides a separation btwn the execution environment i.e cpu and the instance file system. File system can be from the following 3 options.
 - instance store volume -> physically connected, just like harddrive. they can't be reused by other instances, you can't create a snapshot from this type of storage
 - elastic block storage (ebs) -> independent networked volumes. Can be reused by other instances and can live on even after terminating the instance.
 - elastic file system (efs) -> same as ebs only that it is scalable in size.

* ebs backed AMI can be stopped without loosing data unlike instance volume backed AMI that can only be restarted or terminated. It cannot be stopped.
* terminated instances cannot be restarted, stopped instances can be restarted
* ebs can boot faster. instance volume is slower to boot because the AMI data must be transfered from s3.
* ebs volumes are replicated accross multiple availability zones to ensure no data loss
* ebs can be detached and attached to a new instance
* efs can connect to multiple instances at once and is good for big data type apps

AMI visibility -> either public, explicit(visible to anyone with permissions) or implicit(only visible to you).

**ec2 classes**
Difines how the instance live
1.) on demand instances - scale up and down based on demand
2.) reserved instances - you commit for a period of btwn 1-3 yrs and can get upto 75% dicount
3.) spot instances - you bid on and uses spare, unused ec2 capacity. Its good for data processing or research job only becuase it's availability is not garanteed

* AWS sdk methods follow the pascalCase. Not CamelCase

* AMIs are region specific

* AMIs in the market place come with pre-installed softwares e.g. node

* AMIs are supper important as they can help you scale your instances up and down

* we create an AMI from a running instance, the user data does not persist though but you can introduce this from a launch config

2.) you can interact with aws services through their rest APIs, you however have to sign each request using aws credentials and add it as a query param in each request (multiple hashing levels, creating hmac). Because of the complexity of signing, aws created the aws sdk which is much easier to use.

3.) all services and resources in aws have limits that you should be aware of so that you don't get fixed in a corner. e.g. ec2 have limits on the number of running instances per region, also AMIs are stored as an ebs snapshot which have a limit of 10,000, also note that you pay for every ebs snapshot that you have.

4.) scalability is ability to increase or decrease the size / quantity of a resource in aws. elasticity is the ability of your resources to scale in response to stated criteria often triggered by cloudwatch alarms. Therefore scalability is required by elasticity

5.) launch config is a blueprint for creating an ec2 instance. It contains the AMI, instance type, sg and user data. It is used in conjuction with auto scaling groups

* roles gives instance permissions to talk to other services in aws

6.) elb is an essential resource for any elastic web app establishing a single entry point for web request that can handle any size load. 
- A target group is the destination defined where an elb will send requests into. Target groups are connected to auto scalling groups and ends up to be a group of ec2 instances
- listener facilitates sending requests between the elb port and the target group ports
- Auto scaling group are responsible for creating and removing instances from a group according to configured rules
- An auto scaling policy difines when an asg should scale up or down:
   - simple scaling policy - depends on cloudwatch alarm, you need an alarm for each action e.g. one for scaling up and another for scaling down
   - step scaling policy defines  - defines multiple actions per alarm, continuosly performs actions
   - target tracking policies - define metric target, recommended

**limits on autoscaling**
- limits on number of groups and launch configurations
- number of load balancers
- one ssl cert per load balancer
- one target group per load balancer

**s3**
- versioning and lifecycle events
- versioning - s3 stores previous versions of an object any time it is modified. you however will be billed for every version you keep
- s3 life cycle helps manage s3 objects and reduce costs. - life cycle events define rules for objects according to how long since it was created. with life cycle events:
    1. you can move objects to a cheaper storage e.g. s3 infrequent access storage or glacier. you can move previous versions of an object to this kind of storage
    2. object expiration life cycle
- when naming s3 buckets, avoid underscores since they are not allowed in URLs

**limits on ebs and s3**
- ec2 instance must be stopped before removing root ebs volume
- ebs volume can be attached to a running instance apart from those with aws market place product code which must be stopped first
- ebs volumes can only be attached to ec2 instances in the same region
- you can have upto 100 buckets per account
- bucket names must be globally unique

**persistence in aws**
- dynamo db through put capacity is the number of records that can be read or written per second
- dynamo keys and secondary indexes - in order for dynamo to achieve high perfomance, it spreads your data accross many different pertitions. The attribute it uses on each item to decide which pertition to use is called the partition key / hash attribute. It is passed through a hash function that decides which partition to use. Each key must be unique per table
- RDS is great because it takes care of maintenance tasks e.g. automated replicas and backups

- elastic cache provides managed caching services used by web applications. There are two open source cache engines, that is redis and memcached

**persistence limits**
- dynamo - 5 global and 5 local secondary indexes per table, local secondary indexes must be created with the table and cannot be deleted after
- tables with secondary indexes must be created one at a time
- there are limits on number and size of rds databases
- elastic cache has limits on number of clusters and nodes
- you can't access elastic cache outside aws vpc

**route53**
routing policies:
* allows you define the behaviour a routing rule follows once a request comes in
* types: -simple, -weighted, - latency, - geolocation
* concepts: hosted zone (domain name) and record set(dns rule which configures an aws resource as a destination of a request)

**cloudfront distribution**
- cloudfront invalidations
- immutable objects
