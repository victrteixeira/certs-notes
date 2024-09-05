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
# Security Hub
- **Central security tool** to manage security **across several AWS accounts** and **automate security check**
- Integrated dashboards showing current security and compliance status to quickly take actions
- Automatically aggregates alerts in predefined or personal findings formats from various AWS services & AWS partner tools:
	- *Config; GuardDuty; Inspector; Macie*
	- *IAM Access Analyzer; Systems Manager; Firewall Manager*
	- *AWS Health; AWS Partner Network Solutions*
- **Must** first enable the AWS Config Service
![[Pasted image 20240902112450.png]]
## Main Features
- **Cross-Region Aggregation**
	- Aggregate findings, insights, and security scores from multiple Regions to a single aggregation Region
![[Pasted image 20240902112528.png|430]]
- **AWS Organizations Integration**
	- Manage all accounts in the Organization
	- Security Hub automatically detects new accounts
	- By default, Organization management account is the Security Hub Administrator
		- Ability to have a designated Security Hub administrator from member accounts
- **AWS Config Must be enabled**
	- Security Hub uses AWS Config to perform its security checks
	- Must be enabled on all accounts (Security Hub does NOT manage AWS Config)
## Security Standards
- Security Hub generates findings and continuous checks against the rules in a set of supported security standards
- Security Hub supports the following standards:
	- **CIS AWS Foundations;**
	- **PCI DSS;**
	- **AWS Foundational Security Best Practices**
- Ability to enable/disable a security standard
## Integration with GuardDuty
- Automatically enabled when Security Hub is enabled (can be disabled)
- GuardDuty will send findings to Security Hub
- Findings are sent in **AWS Security Finding Format (ASFF)**
![[Pasted image 20240902113248.png|430]]
- Findings are usually sent within 5 minutes
- Archiving a GuardDuty finding will NOT update the finding in Security Hub
## Services Integration
![[Pasted image 20240902113717.png]]
## 3rd Party Integration
- Security Hub integrates with multiple 3rd partner products
- **Send Findings to Security Hub**
	- *3CORESEC; Alert Logic; AQUA*
- **Receive Findings from Security Hub**
	- *Atlassian; FireEye; Fortinet*
- **Update Findings in Security Hub**
	- *Atlassian; ServiceNow*
## Findings
- Security Hub consumes findings using **AWS Security Finding Format (ASFF)** format
- Security Hub automatically updates and deletes findings
	- Findings past 90 days are automatically deleted
- Filter by Region, Integration, Security Standard, Insights
## Insights
- **Collection of related findings** that identifies a security area that requires attention and intervention
	- ==Example==: Insight points out EC2 instances that are subject of findings that detect poor security practices
- Brings findings from across finding providers
- Each Insight defined by a Group By statement and optional Filters
- **Built-In Managed Insights**
	- Return results only if you enabled related product integration or security standard (can not edit or delete)
- **Custom Insights**
	- Track issues specific to your environment
	- ==Example==: Custom Insight to track critical findings affecting member accounts
## Custom Actions
- Helps you automate Security Hub with EventBridge
- Allows you to create actions for response and remediation to **selected findings** within the Security Hub Console
- EventBridge event type is **Security Hub Findings - Custom Action**
- A more specific example of Architecture, remediating a problem found by Macie on a S3 bucket
![[Pasted image 20240902114933.png]]
- A more general example of Architecture following the structure: Detect, Ingest, Remediate and Log
![[Pasted image 20240902114942.png]]
# Detective
- GuardDuty, Macie, and Security Hub are used to identify potential security issues, or findings
- Sometimes security findings require deeper analysis to isolate the root cause and take action - it's a complex process
- Amazon Detective analyzes, investigates, and quickly identifies **the root cause of security issues** or suspicious activities (using ML and graphs)
- **Automatically collects and process events** from VPC Flow Logs, CloudTrail, GuardDuty and create unified view
- Produces visualizations with details and context to get to the root cause
![[Pasted image 20240903104451.png]]
# Penetration Testing on AWS Cloud
- AWS Customers are welcome to carry out security assessment or penetration tests against their AWS infrastructure **without prior approval for 8 services**:
	- *EC2 Instances, NAT Gateways, and Elastic Load Balancers;*
	- *RDS, Aurora;*
	- *Lambda and Lambda Edge Functions;*
	- *Lightsail Resources, Elastic Beanstalk Environments;*
	- *API Gateway, CloudFront*
- **Prohibited Activities**
	- *DNS zone walking via Amazon Route 53 Hosted Zones*
	- *Denial of Service (DoS), Distributed Denial of Service (DDoS), Simulated DoS, Simulated DDoS*
	- *Port Flooding*
	- *Protocol Flooding*
	- *Request Flooding (login request flooding, API request flooding)*
- For any other simulated events, contact aws-security-simulated-event@amazon.com
- https://aws.amazon.com/security/penetration-testing/
# DDoS Simulation Testing on AWS
- Controlled DDoS attack which enables you to evaluate the resiliency of your applications and to practice event response
- Must be performed by an approved **AWS DDoS Test Partner**
- The target can be either **Protected Resources** or **Edge-Optimized API Gateway** that is subscribed to **Shield Advanced**
- Attack must NOT be originated from AWS Resources
- Attack must NOT exceed 20 Gigabits/second
- Attack must NOT exceed 5 million packets/second for CloudFront and 50,000 packets/second for any other service
# Compromised AWS Resources
## Compromised EC2 Instance
### Steps to address compromised instances
- **Capture the instance's metadata**
- **Enable Termination Protection**
- **Isolate the instance**
	- *Replace instance's SG - no outbound traffic authorized*
- **Detach the instance from any ASG**
	- *Suspend processes*
- **Deregister the instance from ELB**
- **Snapshot the EBS volumes**
	- *Deep analysis*
- **Tag the EC2 instance**
	- *e.g. investigation ticket*
- **Offline Investigation**: shutdown the instance
- **Online Investigation**: snapshot memory or capture network traffic
- Automate the isolation process with Lambda
- Automate memory capture with SSM Run Command
![[screenshot-www_udemy_com-2024_09_03-11_12_13.png|450]]
## Compromised S3 Bucket
- Identify the compromised S3 bucket using **GuardDuty**
- Identify the source of the malicious activity (e.g. IAM user, role) and the API calls using **CloudTrail or Amazon Detective**
- Identify whether the source was authorized to make those API calls
- Secure your S3 bucket with recommended settings:
	- *S3 Block Public Access Settings; S3 Bucket Policies and User Policies; VPC Endpoints for S3; S3 Pre-signed URLs; S3 Access Points; S3 ACLs*
![[screenshot-www_udemy_com-2024_09_03-11_16_35.png|410]]
## Compromised ECS Cluster
- Identify the affected ECS Cluster using **GuardDuty**
- Identify the source of the malicious activity (e.g. container image, tasks)
- Isolate the impacted tasks
	- *Deny all ingress/egress traffic to the task using security groups*
- Evaluate the presence of malicious activity (e.g. malware)
![[screenshot-www_udemy_com-2024_09_03-11_16_35 1.png|400]]
## Compromised Standalone Container
- Identify the malicious container using **GuardDuty**
- Isolate the malicious container
	- *Deny all ingress/egress traffic to the container*
- Suspend all process in the container
	- *Pause the container*
- Stop the container and look at EBS Snapshots retained by GuardDuty (snapshot retention feature)
- Evaluate the presence of malicious activity (e.g. malware)
![[screenshot-www_udemy_com-2024_09_03-11_24_10.png|410]]
## Compromised RDS Database Instance
- Identify the affected DB instance and DB user using **GuardDuty**
- If it is NOT legitimate behavior
	- *Restrict network access (SGs & NALCs)*
	- *Restrict the DB access for the suspected DB user*
- Rotate the suspected DB users' passwords
- Review DB Audit Logs to identify leaked data
- Secure your RDS DB instance with recommended settings
	- *Use Secrets Manager to rotate the DB password*
	- *Use IAM DB Authentication to manage DB users' access without passwords*
![[screenshot-www_udemy_com-2024_09_03-11_28_33.png|400]]
# Compromised AWS Credentials
## Credentials
- Identify the affected IAM user using **GuardDuty**
- Rotate the exposed AWS Credentials
- Invalidate temporary credentials by **attaching an explicit Deny policy** to the affected IAM user with an STS date condition
- Check **CloudTrail Logs** for other unauthorized activity
- Review your AWS resources (e.g. delete unauthorized resources)
- Verify your AWS account information
![[screenshot-www_udemy_com-2024_09_03-11_35_52.png|400]]
## Roles
- Invalidate temporary credentials by **attaching an explicit Deny policy** to the affected IAM user with an STS date condition
- Revoke access for the identity to the linked AD if any
- Check **CloudTrail logs** for other unauthorized activity
- Review your AWS resources (e.g. delete unauthorized resources)
- Verify your AWS account information
![[screenshot-www_udemy_com-2024_09_03-11_38_46.png|400]]
## Account
- Rotate and delete exposed AWS Access Keys
- Rotate and delete any unauthorized IAM user credentials
	- *Rotate existing IAM users' password*
- Rotate and delete all EC2 Key Pairs
- Check **CloudTrail Logs** for other unauthorized activity
- Review your AWS resources
	- *Delete unauthorized resources*
- Verify your AWS account information
![[screenshot-www_udemy_com-2024_09_03-11_41_50.png|400]]
# EC2 Key Pairs
- When you create a key pair, the **private key** is downloaded then automatically deleted from AWS, and the **public key** is stored in `~/.ssh/authorized_keys` inside your EC2 instance
- There's no way to recover a private key if lost
- Key Pairs can be created outside of AWS and uploaded to there
- Both ED25519 and 2048-bit SSH-2 RSA keys are supported
- Key Pairs don't get deleted from EC2 instance's root volumes when the Key Pair removed from the EC2 Console
- Launching an EC2 instance with prebuilt AMI, the old Key Pair will exist alongside with the new Key Pair
![[screenshot-www_udemy_com-2024_09_04-11_43_04.png|400]]
## Remediating Exposed EC2 Key Pairs
- Remove all the public keys in `~/.ssh/authorized_keys` file on EC2 instances
- Create a new Key Pair and add its public key to the `~/.ssh/authorized_keys` file on all EC2 instances
- ==Note:== Use SSM Run Command to automate the process add/delete public keys on EC2 instances
![[screenshot-www_udemy_com-2024_09_04-11_44_55.png]]
# EC2 Instance Connect Diagram
![[screenshot-www_udemy_com-2024_09_04-11_48_28.png]]
# EC2 Serial Console
- ==Use Cases==: **troubleshoot boot, troubleshoot network configuration, analyze reboot issues**
- Directly access your EC2 instance's portal (as if keyboard and monitor are directly attached to the EC2 instance)
- Does NOT require any network capabilities
- Use with supported Nitro-based EC2 instances
- **Must setup OS User and Password**
- Only one active session per EC2 instance
- Disabled by default (enable at AWS account level)
## Perplexity Answer
The **Amazon EC2 Serial Console** is a feature that provides a secure and interactive way to troubleshoot issues with Amazon EC2 instances. It allows users to connect directly to the serial port of an instance, enabling them to run diagnostic commands as if they were physically connected to the instance with a monitor and keyboard. This feature is particularly useful for resolving boot and network connectivity problems when standard access methods like SSH or RDP are unavailable.
### Key Features

- **Direct Access**: The serial console allows for one-click, text-based access to an instance's serial port, which is essential for troubleshooting issues that prevent normal access to the instance[1](https://aws.amazon.com/about-aws/whats-new/2024/01/amazon-ec2-serial-console-available-local-zones/)[2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-serial-console.html).
- **No Networking Required**: Unlike typical remote connections, the serial console does not require the instance to have networking capabilities, making it ideal for instances that are misconfigured or experiencing boot failures[3](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-serial-console.html)[4](https://sysdig.com/blog/ec2-serial-console-enabled/).
- **Interactive Commands**: Users can enter commands directly into the console, which is beneficial for diagnosing problems in real-time. This capability includes viewing boot messages during instance reboots[5](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-serial-console.html)[6](https://aws.amazon.com/about-aws/whats-new/2021/03/introducing-ec2-serial-console/).
- **Security and Access Control**: Access to the EC2 Serial Console is not enabled by default. Organizations must explicitly grant access through IAM policies, allowing for granular control over who can use this feature. This includes the ability to restrict access based on instance IDs or resource tags[7](https://clouxter.com/en/blog/amazon-ec2-serial-console-in-all-aws-local-zones/)[8](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-serial-console.html)[9](https://sysdig.com/blog/ec2-serial-console-enabled/).
- **Availability**: The EC2 Serial Console is available for instances using the Nitro System, which is a hardware-accelerated virtualization platform used by AWS. It is supported across various AWS regions and is available at no additional cost[10](https://aws.amazon.com/about-aws/whats-new/2024/01/amazon-ec2-serial-console-available-local-zones/)
# Lost EC2 Key Pair
## Linux
### EC2 User Data
- Create a new Key Pair, then copy the public key
- Stop the instance, update the EC2 User Data (cloud-config format)
- Start the instance and connect with the private key
- Delete the EC2 User Data
![[screenshot-www_udemy_com-2024_09_04-12_18_32.png]]
### Systems Manager
- Use **AWSSupport-ResetAccess** Automation Document
- Will create and apply a new key pair
- Works for both **Linux and Windows**
- The private key stored encrypted in SSM Parameter Store:
	- */ec2rl/openssh/**instance_id**/key*
- Must have SSM Agent installed in the instance
![[screenshot-www_udemy_com-2024_09_04-12_25_58.png|450]]
### Instance Connect
- EC2 Instance Connect agent must be installed
	- *Already installed on Amazon Linux 2 and Ubuntu 16.04 or later*
- Connect using EC2 Instance Connect temporary session
- Store permanent new public SSH key into `~/.ssh/authorized_keys`
![[screenshot-www_udemy_com-2024_09_04-12_33_18.png]]
- **PS**: It's possible to connect with the Serial Console to do that too: [[Threat Detection and Incident Response#EC2 Serial Console|1]]
### EBS Volume Swap
- Create a new Key Pair
- Stop the original EC2 Instance
- Detach the EBS root volume
- Attach the EBS volume to a temporary EC2 instance as a secondary volume
- Add the new public key to `~/.ssh/authorized_keys` on the volume
- Re-attach the volume to the original instance, then restart the instance
![[screenshot-www_udemy_com-2024_09_04-12_40_44.png|410]]
## Windows
### EC2 Launch v2
- Verify EC2Launch v2 service is running
	- *Windows AMIs with the EC2Launch v2 service*
- Detach the EBS root volume
- Attach the volume to a temporary instance as a secondary volume
- Delete file *%ProgramData%/Amazon/EC2Launch/state/.run-once*
- Re-attach the volume to the original instance, then restart the instance, you will be able to set a new password
![[screenshot-www_udemy_com-2024_09_04-12_44_57.png|400]]
### EC2Config
- Verify EC2Config service is running
- **Windows AMIs before Windows Server 2016**
- Detach the EBS root volume
- Attach the volume to a temporary instance as a secondary volume
- Modify the file *\Program Files\Ec2ConfigService\Settings\config.xml*
- Set **EC2SetPassword to Enabled**
- Reattach the volume to the original instance, then restart the instance
![[screenshot-www_udemy_com-2024_09_04-12_46_38.png|400]]
### EC2Launch
- **Windows Server 2016 and later AMIs that doesn't include EC2Launch v2**
- Detach the EBS root volume
- Attach the volume to a temporary instance as a secondary volume
- Download and install **EC2Rescue Tool for Windows Server**
- Select **Offline Instance Option -> Diagnose and Rescue -> Reset Administrator Password**
- Reattach the volume to the original instance, then restart the instance
![[screenshot-www_udemy_com-2024_09_04-12_50_00.png|400]]
### Systems Manager
- **Must have SSM Agent installed**
- **Method 1**
	- Use *AWSSupport-RunEC2RescueForWindowsTool* Run Command Document
	- Install and run **EC2Rescue Tool** for Windows Server
	- Command is set to **ResetAccess**
- **Method 2**
	- Use *AWSSupport-ResetAccess* Automation Document
	- Works for both Linux and Windows
- **Method 3**
	- Manually *AWS-RunPowerShellScript* Run Command Document
	- Command: **net user Administrator Password@123**
![[screenshot-www_udemy_com-2024_09_04-12_53_27.png|400]]
# EC2Rescue Tool for Windows & Linux
## Linux
- Diagnose and troubleshoot common issues
- Gather syslog logs, diagnose problematic kernel parameters, diagnose common OpenSSH issues
- Supports over 100 modules
- Amazon Linux 2, Ubuntu, RHEL, Suse LInux, etc.
- Install manually or using **AWSSupport-TroubleshootSSH** Automation Document
- Upload the results directly to AWS Support or an S3 Bucket
### Use Cases
- ==Collect System Utilization Reports==: *vmstate, iostat, mpstat, etc*
- ==Collect Logs and Details==: *syslog, dmesg, application error logs, and SSM logs*
- ==Detect System Problems==: *Asymmetric routing or duplicate root device labels*
- ==Automatically Remediate System Problems==: *Correcting OpenSSH file permissions; Disabling known problematic kernel problems*
- ==You can create your own custom module==
## Windows
- Diagnose and troubleshoot common issues
- Collect log files, troubleshoot issues, provide suggestions, …
- Supports 2 modules (data collector, analyzer)
- Windows Server 2008 R2 or later
- Install manually or using **AWSSupport-RunEC2RescueForWindowsTool** Run Command Document
    - ==Commands==: *CollectLogs, FixAll, ResetAccess*
- Use **AWSSupport-ExecuteEC2Rescue** Automation Document to troubleshoot connectivity issues
- Upload the results directly to an S3 bucket
### Use Cases
- ==Instance Connectivity Issues==: *Firewall, RDP, or network interface configuration*
- ==OS Boot Issues==: *Blue screen or stop error, a boot loop, or a corrupted registry*
- ==Gather OS Logs and Configuration Files==: *If you need advanced log analysis and troubleshooting*
- ==Common OS Issues==: *Disk signature collision*
- ==Perform a restore==
# Acceptable Use Policy (AUP)
-  Governs your use of the services offered by AWS
- You may not use for
	- *Illegal or fraudulent activity*
	- *Violate the rights of others*
	- *Threaten, terrorism, violence, or other serious harm*
	- *Child sexual abuse content or activity*
	- *Violate the security, integrity or availability for other networks and computers*
	- *Distribute, publish or facilitate the unsolicited mass emails (e.g. spams)*
- AWS will remove or disable any content that violates this policy
# Abuse Report
- When you suspect that AWS resources are used for abusive or illegal purposes
- You can create an AWS Abuse report
- Examples: spam, port scanning, DDoS attacks, intrusion attempts, hosting prohibited content, distributing malware
- Contact **AWS Trust & Safety Team** with details (e.g. logs, email headers, etc)
- If you receive an email that your AWS resources are used for illegal activity
	- *Respond to the email and explain how you're preventing this*
	- *If you don't respond within 24 hours, AWS might suspend your AWS account*
# IAM Security Tools
- **IAM Credentials Report (*account-level*)**: a report that lists all your account's users and the status of their various credentials
- **IAM Access Advisor (*user-level*)**: Access advisor shows the service permissions granted to a user and when those services were last accessed; You can use this information to revise your policies
# IAM Access Analyzer
- Find out which resources are shared externally
	- *S3 Buckets; IAM Roles; KMS Keys; Lambda Functions and Layers; SQS queues; Secrets Manager Secrets*
- Define **Zone of Trust = AWS Account or AWS Organization**
- Access outside zone of trust will generate findings
![[screenshot-www_udemy_com-2024_09_04-13_20_09.png|400]]
- **IAM Access Analyzer Policy Validation**
	- Validates your policy against IAM policy grammar and best practices
	- General warnings, security warnings, errors, suggestions
	- Provides actionable recommendations
- **IAM Access Analyzer Policy Generation**
	- Generates IAM policy based on access activity
	- CloudTrail logs is reviewed to generate the policy with the **fine-grained permissions and the appropriate Actions and Services**
	- Reviews CloudTrail logs for up to 90 days