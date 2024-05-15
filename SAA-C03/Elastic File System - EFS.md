- Use cases: content management, web serving, data sharing, Wordpress
- Uses NFSv4.1 protocol
- Uses security group to control access to EFS
- **Compatible with Linux based AMI (not Windows)**
- Encryption at rest using KMS
- POSIX file system (~Linux) that has a standard file API
- File system scales automatically, pay-per-use, no capacity planning

## Performance
### EFS Scale
- 1000s of concurrent NFS clients, 10GB+/s throughput
- Grow to Petabyte-scale network file system, automatically

### Performance Mode (set at EFS creation time)
- General Purpose (default) - latency-sensitive use cases (web server, CMS, etc...)
- Max I/O - higher latency, throughput, highly parallel (big data, media processing)

### Throughput Mode
- Bursting - 1 TB = 50MiB/s + burst of up to 100MiB/s
- Provisioned - set your throughput regardless of storage size, ex: 1 GiB/s for 1 TB storage
- Elastic - automatically scales throughput up or down based on your workloads
	- Up to 3GiB/s for reads and 1GiB/s for writes
	- Used for unpredictable workloads


## Storage Classes
### Storage Tiers
- Lifecycle management feature - move file ater N days
- Standard: for frequently accessed files
- Infrequent Access (EFS-IA): cost to retrieve files, lower price to store. Enable EFS-IA with a Lifecycle Policy

### Availability and Durability
- Standard: Multi-AZ, great for prod
- One Zone: One AZ, great for dev, backup enabled by default, compatible with IA
	- EFS One Zone-IA: EFS for infrequent access files, and combined with one availability zone
	- Over 90% in cost saving