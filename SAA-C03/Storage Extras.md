# FSx Overview
- Launch 3rd party high-performance file systems on AWS
- Fully managed service

## FSx for Windows (File Server)
- Fully managed Windows file system share drive
- Supports SMB protocol & Windows NTFS
- Microsoft Active Directory integration, ACLs, user quotas
- **Can be mounted on Linux EC2 instances**
- Supports Microsoft's Distributed File System (DFS) Namespaces (group files across multiple FS)
- Can be accessed from your on-premises infrastructure (VPN or Direct Connect)
- Can be configured to be Multi-AZ (high availability)
- Data is backed-up daily to S3

## FSx for Lustre
- Lustre is a type of parallel distributed file system for **large-scale computing**.
- Lustre means "Linux" and "Cluster"
- Machine Learning, **High Performance Computing (HPC)** 
- Seamless integration with S3
	- Can "read S3" as a file system (through FSx)
	- Can write the output of the computations back to S3 (through FSx)
- Can be used from on-premises servers using VPN or Direct Connect

## File System Deployment Options
### Scratch File System
- Temporary storage
- Data is not replicated (doesn't persist if file server fails)
- High burst (6x faster, 200MBps per TiB)
- Usage: short-term processing ,optimize costs
### Persistent File System
- Long-term storage
- Data is replicated within same AZ
- Replace failed files within minutes
- Usage: long-term processing, sensitive data

# Transfer Family
- Fully-managed service for file transfers into and out of Amazon S3 or Amazon EFS using the **FTP Protocol**
- Managed infrastructure, scalable, reliable, highly available (multi-AZ)
- Pay per provisioned endpoint per hour + data transfers in GB
- Store and manage users' credentials within the service
- **EXAM TIP**: Transfer Family é utilizado para transferências de dados utilizando FTP protocol

# DataSync
- Move large amount of data to and from places
	- On-premises / Other cloud to AWS (NFS, SMB, HDFS, S3 API) - needs agent
	- AWS to AWS (different storage services) - no agent needed
- Can synchronize to S3, EFS and FSx (Windows, Lustre, NetApp, OpenZFS)
- Replication tasks can be scheduled hourly, daily, weekly
- **File permissions and metadata are preserved** (NFS POSIX, SMB, ...)
- One agent task can use 10Gbps, can setup a bandwidth limit
- **EXAM TIP:** Se um servidor on-premise ou em cloud não tem a largura de banda necessária para transferência, é possível utilizar um Snowcone com DataSync Agent pre-instalado para realizar a transferência