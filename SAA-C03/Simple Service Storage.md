# Security
I. User-Based
- IAM Policies: which API calls should be allowed for a specific user from IAM
II. Resource-Based
- Bucket Policies: bucket wide rules from the S3 Console - allows cross account
- Object Access Control List (ACL): finer grain (can be disabled)
- Bucket Access Control List (ACL): less common (can be disabled)
**OBS: an IAM principal can access an S3 Object if:
i. The user IAM permissions ALLOW it OR the resource policy ALLOWS it
ii. AND there's no explicit DENY**

# Replication (CRR & SRR)
- CRR - Cross-Region Replication;
- SRR - Same-Region Replication;
- Must enable Versioning in source and destination buckets
- Buckets can be in different AWS accounts
- Copying is **asynchronous**
- Must give proper IAM permissions to S3
- After enable Replication, only **new** objects are replicated
	- Optionally, you can replicate existing objects using S3 Batch Replication: it replicates existing objects and objects that failed replication
- For DELETE operations: you can replicate **delete markers** from source to target (optional setting)
	- Although, deletions with a version ID are not replicated (to avoid malicious deletes)
- Use Cases:
	- CRR - compliance, lower latency access, replication across accounts
	- SRR - log aggregation, live replication between production and test accounts

# Storage Classes
- S3 Standard - General Purpose
- S3 Standard-Infrequent Access (IA)
- S3 One Zone-Infrequent Access
- S3 Glacier Instant Retrieval
- S3 Glacier Flexible Retrieval
- S3 Glacier Deep Archive
- S3 Intelligent Tiering

- Objects can be moved between classes manually or using S3 Lifecycle configurations

# Upload Performance
### Multi-Part Upload
- Recomendado para arquivos maiores que 100MB e obrigatório para arquivos a partir de 5GB
### Transfer Accelerator
- Envia o arquivo para uma edge location e utiliza-se da rede interna da AWS para acelerar o envio do arquivo
- Compatível com multi-part upload