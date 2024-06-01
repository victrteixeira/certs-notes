# High Availability (HA)
- Ability of a system to remain operational to users during planned or unplanned outages
	- Planned outages examples: **operating system security patches; application updates; hardware replacement; migrating to a new hosting provider**
	- Unplanned outages examples: **hardware failure; network disruptions; power outages; natural disasters; cyber attacks; software bugs; poor scaling / architecture design**
## Methods to Mitigate Planned Outages
- Gradual deployment strategy
	- 1-10-100-etc
- Testing and monitoring of deployment
- Easy rollback plan
- Small and frequent deployments
- Every single core component has redundancy
- Use Azure's built-in features for availability
	- Availability Sites
	- Availability Zones
	- Cross-Region Load Balancing / Front Door
- Constant health monitoring / probes
- Strong security practices
- Have a disaster recovery plan
	- Test that disaster recovery plan / fire drills
- Load testing

## Sum Up
- A conscious effort to avoid the obvious sources of downtime

# Scalability
- The ability of a system to accommodate increasing demand by adding or removing resources as needed
- It allows a system to adapt to changing usage patterns and handle increased traffic without requiring changes to the application code or system design.

## Vertical Scaling
- Also called "scaling up" or "scaling down"
- Adding more resources to a single server
- Increase the amount of memory, the number of CPUs
- There is an upper limit to this
	- Azure - 96 vCPUs, 384 GB memory
- Does not improve availability

## Horizontal Scaling
- Also called "scaling out" or "scaling in"
- Adding more servers to a system
- No limits to scaling
- Additional complexities for load balancing

## Impact on System Cost
- Adding more sources to a system adds to cost
- Reducing resources can reduce cost
- Having a scalable system allows for a system to be perfectly sized

# Elasticity
- The ability of a system to quickly and easily scale up or down the amount of resources that a system uses in response to changing demand
- Often called "autoscaling" in cloud computing
- The system monitors some metric (such as CPU utilization) to determine how busy a system is
- Adds resources when it exceeds a limit for being busy
- Remove resources when it falls below a limit for being not busy
- Elasticity is more efficient and cost-effective use of resources
- Minimizes computing "waste" - resources paid for and not used
- Self-hosted system tend to have a large percentage of "over-provisioned" resources for anticipated future growth

# Reliability
- The ability of a system to perform its intended function without interruption and with a high degree of accuracy
## Availability vs Reliability
- A system can be highly available to users
- In that it responds instantly to every request
- The system itself might be highly unreliable
	- What use is a calculator that can answer every question with the wrong answer?
	- An app that loses your data sometimes randomly
## Why is it needed?
- You have to trust that your cloud provider is doing everything it can to make its platform reliable
- This includes transparency during service issues
## How is it achieved?
- Auto-scaling (Elasticity)
- Multi-region deployments
- Data backup and replication
- Health probes and self-healing
# Predictability
- The ability to forecast and control the performance and behavior of a system
- Includes the ability to predict future costs
## Why is it needed?
- Predictability gives you the confidence that the system will continue to perform at the expected level in the future.
- And of course, that you won't get a crazy bill unexpectedly
## How is it achieved?
- Auto-scaling (Elasticity)
- Load balancing
- Different instance types, sizes, pricing tiers
- Cost management tools
- API
- Pricing calculators

# Security
- Security is a full-time job
- Cloud providers are obviously massive targets for hackers, and so they rightly spend a lot of time, money and effort on platform security
- Cloud providers go through security audits and compliance certifications
- And provide customers (you) the tools they need to enable and monitor security with their own applications/data
## Why is it needed?
- Security is a fundamental challenge in IT
- You want confidence that your cloud provider cannot easily be defeated by hackers and those with malicious intent
## Why is it achieved?
- Industry standard compliance certifications
- Microsoft Security Response Center (MSRC)
- Always-on DDoS
- Azure Policy & Blueprint
- Role based access control (RBAC)
- Azure Active Directory
- Always up-to-date platform services
- Update management
- Encryption by default
- Dozens of security services like firewall
# Governance
- How your organization does business
- The process of defining, implementing, and monitoring a framework of policies that guides an organization's cloud operations
## Why is it needed?
- Your company wants to ensure it's policies are followed in the cloud
- Includes basic auditing and reporting, as well as enforcement
- You want to be compliant with industry standards such as HIPAA or PCC or GDPR
## How is it achieved?
- Azure Policy & Blueprint
- Management groups
- Custom roles
- Soft deletes
- Guides and best practices such as Cloud Adoption Framework
# Manageability
- Management of the cloud
- Management in the cloud
## Management of the cloud
- Templates
- Automation
- Scaling
- Monitoring and alerts
- Self-healing
## Management in the cloud
- Web portal
- Command line interface and scripts
- APIs
- PowerShell
## Why is it needed?
- How easy it is to work with your applications in the cloud impacts cost, performance, security and other priorities
- Different cloud vendors are going to be easier or harder to work with
## How is it achieved?
- Azure Portal, CLI, PowerShell, Cloud Shell, REST APIs, and other programmatic methods
- Consolidated monitoring and alerting system
- Ability to use ARM templates, Bicep, Terraform, etc.
- Autoscaling of most types of computes resources