# Instances Purchasing Options
- On-Demand - short workload, predictable pricing, pay by second
- Reserved (1 & 3 years)
	- Reserved Instances - long workloads
	- Convertible Reserved Instances - long workloads with flexible instances
- Saving Plans (1 & 3 years) - commitment to an amount of usage, long workload
- Spot Instances - short workloads, cheap, can lose instances (less reliable)
- Dedicated Hosts - book an entire physical server, control instance placement
- Dedicated Instances - no other customers will share your hardware
- Capacity Reservations - reserve capacity in a specific AZ for any duration

## On-Demand
- Pay for what you use
	- Linux or Windows - billing per second, after the first minute
- Has the highest cost but no upfront payment
- No long-term commitment
- Recommended for **short-term** and **un-interrupted workloads**, where you can't predict how the application will behave

## Reserved Instances
- Up to 72% discount
- You reserve a specific instance attribute (**Instance Type, Region, Tenancy, OS**)
- Reservation Period - 1 year (+ discount) or 3 years ( +++ discount)
- Payment Options - No Upfront (+), Partial Upfront (++), All Upfront (+++)
- Reserved Instance's Scope - Regional or Zonal (reserve capacity in an AZ)
- Recommended for **steady-state** usage applications (think database)
- Buy and sell in the Reserved Instance Marketplace
- Convertible Reserved Instance
	- Up to 62% discount
	- Can change the EC2 instance type, instance family, OS, scope and tenancy

## Saving Plans
- Get a discount based on long-term usage
- Commit to a certain **type of usage** ($10/hour for 1 or 3 years)
- Usage beyond EC2 Saving Plans is billed at the On-Demand price
- Locked to a specific instance family & AWS Region (e.g. M5 in us-east-1)
- Flexible across
	- Instance Size (e.g. m5.xlarge, m5.2xlarge)
	- OS (e.g. Linux, Windows)
	- Tenancy (Host, Dedicated, Default)

## Spot Instances
- Up to 90% discount
- Instance that you can "lose" at any point of time if your max price is less than the current spot price
- Useful for workloads that are resilient to failure
	- Batch jobs
	- Data analysis
	- Image processing
	- Any **distributed** workloads
	- Workloads with a flexible start and end time

## Dedicated Hosts
- A physical server with EC2 instance capacity fully dedicated to your use
- Allows you address **compliance requirements** and **use your existing server-bound software licenses** (per-socket, per-core, per-VM software license)
- Purchasing Options
	- On-demand
	- Reserved
- The most expensive option
- Useful for software that have complicated licensing model (BYOL - Bring Your Own License), or for companies that have strong regulatory or compliance needs

## Dedicated Instances
- Instances run on hardware that's dedicated to you
- May share hardware with other instances in same account
- No control over instance placement (can move hardware after Stop / Start)

## Capacity Reservations
- Reserve **On-Demand** instances capacity in a specific AZ for any duration
- You always have access to EC2 capacity when you need it
- **No time commitment** (create/cancel anytime), **no billing discounts**
- Combine with Regional Reserved Instances and Savings Plans to benefit from billing discounts
- You're charged at On-Demand rate whether you run instances or not
- Suitable for short-term, uninterrupted workloads that needs to be in a specific AZ

# EBS Volumes
- Elastic Block Store Volume is a **network** drive you can attach to your instances while they run
- It allows your instances to persist data, even after their termination
- They are bound to a specific availability zone
- Analogy: think of them as a "network USB stick"
- It's a network drive (i.e. not a physical drive)
	- It uses the network to communicate the instance, which means there might be a bit of latency
	- It can be detached from an EC2 instance and attached to another one quickly
- Locked to an Availability Zone (AZ)
	- An EBS volume in **us-east-1a** cannot be attached to **us-east-1b**
	- To move a volume across, you first need to snapshot it
- Have a provisioned capacity (size in GBs, and IOPS)
	- You get billed for all the provisioned capacity
	- You can increase the capacity of the drive over time

## EBS Snapshot
### Snapshot Archive
- Move a snapshot to an "archive tier" that is 75% cheaper
- Takes within 24 to 72 hours for restoring the archive
### Recycle Bin for EBS Snapshots
- Setup rules to retain deleted snapshots so you can recover them after an accidental deletion
- Specify retention (from 1 day to 1 year)
### Fast Snapshot Restore (FSR)
- Force full initialization of snapshot to have no latency on the first use
- But it costs a lot of money

# Instance Store
- EBS volumes are network drives with good but "limited" performance
- **If you need a high-performance hardware disk, use EC2 Instance Store**
- Better I/O performance
- EC2 Instance Store lose their storage if they're stopped (ephemeral)
- Good for **buffer / cache / scratch data / temporary content**
- Risk of data loss if hardware fails
- Backups and Replication are your responsibility

# EBS Multi-Attach
- io1/io2 family only
- Attach the same EBS volume to multiple EC2 instances in the same AZ
- Each instance has full read & write permissions to the high-performance volume
- Use case:
	- Achieve **higher application availability** in clustered Linux applications (ex: Teradata)
	- Applications must manage concurrent write operations
- **Up to 16 EC2 Instances at a time**
- Must use a file system that's cluster-aware (not XFS, EXT4, etc)

# EBS Encryption
- When you create an encrypted EBS volume, you get the following:
	- Data at rest is encrypted inside the volume
	- All the data in flight moving between the instance and the volume is encrypted
	- All snapshots are encrypted
	- All volumes created from the snapshot are encrypted
- Encryption and decryption are handled transparently (you have nothing to do)
- EBS Encryption leverages keys from KMS (AES-256)
- Copying an unencrypted snapshot allows encryption
- Snapshots of encrypted volumes are encrypted
