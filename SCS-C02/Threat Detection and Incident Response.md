# GuardDuty
- Intelligent Threat discovery to protect your AWS account
- Uses machine learning algorithms, anomaly detection, 3rd party data
- One click to enable (30 days trial, no need to install software
- **Input Data** includes:
	- **CloudTrail Events Logs**: unusual API calls, unauthorized deployments
		- ==CloudTrail Management Events==: create VPC subnet, create trail, etc.
		- ==CloudTrail S3 Data Events==: get object, list objects, delete object, etc.
	- **VPC Flow Logs**: unusual internal traffic, unusual IP address
	- **DNS Logs**: compromised EC2 instances sending encoded data within DNS queries
	- **Optional Features**: EKS Audit Logs, RDS & Aurora, EBS, Lambda, S3 Data Events
- Can setup EventBridge rules to be notified in case of findings
- EventBridge rules can target AWS Lambda or SNS
- Can protect against **CryptoCurrency** attacks (has a dedicated "finding" for it)
![[Pasted image 20240901113833.png]]
## Multi-Account Strategy
- You can manage multiple accounts in GuardDuty
- Associate the Member accounts with the Administrator account
	- Through an AWS Organization
	- Sending invitation through GuardDuty
- Administrator account can
	- Add and remove member accounts
	- Manage GuardDuty within the associated member accounts
	- Manage findings, suppression rules, trusted IP lists, threat lists
- In an AWS Organization, you can specify a member account as the Organization's **delegated administrator for GuardDuty**
![[Screenshot 2024-09-01 114230.png]]
## Findings Automated Response
- Findings are potential security issue for malicious events happening in your AWS account
- Automate response to security issues revealed by GuardDuty Findings using EventBridge
- Send alerts to SNS (email, Lambda, Slack, etc)
- Events are published to both the administrator account and the member account that it is originated from
![[Pasted image 20240901114816.png|400]]
## Findings
- **GuardDuty pulls independent streams of data directly from CloudTrail logs (management events, data events), VPC Flow Logs or EKS logs**
	- It will read your VPC flow logs and your EKS logs and you do not have to enable these. GuardDuty is going to get this data itself from its sources independently by using the capability within your AWS cloud.
- Each finding has a severity value between 0.1 to 8+ (High, Medium, Low)
- Finding Name Convention: *ThreatPurpose:ResourceTypeAffected/ThreatFamilyName.DetectionMechanism!Artifact*
	- ==ThreatPurpose==: primary purpose of the threat (e.g. Backdoor, CryptoCurrency)
	- ==ResourceTypeAffected==: which AWS resource is the target (e.g. EC2, S3)
	- ==ThreatFamilyName==: describes the potential malicious activity (e.g. NetworkPortUnusual)
	- ==DetectionMechanism==: method GuardDuty detecting the finding (e.g. TCP, UDP)
	- ==Artifact==: describes a resource that is used in the malicious activity (e.g. DNS)
### Finding Types
- ==EC2 Finding Types==: *UnauthorizedAccess:EC2/SSHBruteForce, CryptoCurrency:EC2/BitcoinToo.B!DNS*
- ==IAM Finding Types==: *Stealth:IAMUser/CloudTrailLoggingDisabled, Policy:IAMUser/RootCredentialUsage*
- ==Kubernetes Audit Logs Finding Types==: *CredentialAccess:Kubernetes/MaliciousIPCaller*
- ==Malware Protection Finding Types==: *Execution:EC2/SuspiciousFile, Execution:ECS/SuspiciousFile*
- ==RDS Protection Finding Types==: *CredentialAccess:RDS/AnomalousBehavior.SuccessfulLogin*
- ==S3 Finding Types==: *Policy:S3/AccountBlockPublicAccessDisabled, PenTest:S3/KaliLinux*
## Trusted and Threat IP Lists
- Works only for public IP addresses
- **Trusted IP List** 
	- List of IP addresses and CIDR ranges that you trust
	- GuardDuty **does not** generate findings for these trusted lists
- **Threat IP List**
	- List of known malicious IP addresses and CIDR ranges
	- GuardDuty **generates** findings based on these threat lists
	- Can be supplied by 3rd party threat intelligence or created custom for you
- In a multi-account GuardDuty setup, only the GuardDuty **administrator account can manage those lists**
## Suppression Rules
- Set of criteria that **automatically filter and archive new findings**
	- *Example: low-value findings, false-positive findings, or threat you don't intend to act on*
- Can suppress entire findings types or define more granular criteria (e.g. suppress only specific EC2 instances)
- Suppressed findings **are not** sent to Security Hub, S3, Detective, or EventBridge
## Troubleshooting:GuardDuty didn't generate any findings
- ==Problem==: GuardDuty is activated but it didn't generate any DNS based findings
- ==Reason==: GuardDuty only processes DNS logs if you use the default VPC DNS resolver. All the other types of DNS resolvers won't generate DNS based findings
- If GuardDuty is suspended or disabled, then no finding types are generated
- Best practice to enabled GuardDuty even if in the regions you don't use