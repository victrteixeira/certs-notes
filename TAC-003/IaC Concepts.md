# What is Infrastructure as Code (IaC)
- Manually configuration is an excellent way to start new services, but it comes with many downsides
	- Easy to mis-configure a service though human error
	- It's hard to manage the expected state of configuration for compliance
	- It's hard to transfer configuration knowledge to other team members
- IaC allows to write a configuration script to automate creating, updating or destroying any cloud infrastructure
	- IaC is a blueprint of infrastructure
	- IaC allows to share, version or inventory cloud infrastructure

# Popular IaC Tools
1. Declarative
   - What you see is what you get - **Explicit**
   - More verbose, although zero chance of mis-configuration
   - Uses scripting languages e.g. JSON, YAML, XML
   - Examples
	   - ARM Templates;
	   - Azure Blueprints;
	   - CloudFormation;
	   - Cloud Deployment Manager;
	   - Terraform
1. Imperative
   - You say what you want, and the rest is filled in - **Implict**
   - Less verbose, you could end up with misconfiguration
   - Does more than Declarative
   - Uses programming languages e.g. Python, Ruby, JavaScript
   - Examples
	   - AWS Cloud Development Kit (CDK)
	   - Pulumi

# Infrastructure Lifecycle
- Infrastructure lifecycle is a number of clearly defined and distinct work phases which are used by DevOps engineers to plan, design, build, test, deliver, maintain and retire cloud infrastructure
- Common SDLC nomenclature is about Day 0, Day 1, and Day 2
	- Day 0-2 is a simplified way to describe phases of an infrastructure lifecycle
		- Day 0 - Plan and Design
		- Day 1 - Develop and Iterate
		- Day 2 - Go live and maintain
	- Days don't literally mean a 24 hour days, it is just a way of defining where a Infrastructure project would be in its process of development.
## Advantages
- Reliability
	- IaC makes changes *idempotent*, consistent, repeatable, and predictable.
		- Idempotent means no matter how many times you run IaC, you'll always end up with the same state that is expected
- Manageability
	- Enable mutation via code
	- Revised with minimal changes
- Sensibility
	- Avoid financial and reputational losses to even loss of life when considering government and military dependencies on infrastructure

# Configuration Drift
- Configuration drift is when provisioned infrastructure has an unexpected configuration change due to manually adjusted configuration options, malicious actors or side affects from APIs, SDK or CLIs
- Drifts going unnoticed could be loss or breach of cloud services and residing data or result in interruption of services or unexpected downtime.

# GitOps
- GitOps is when you take IaC and you use a git repository to introduce a formal process to review and accept achanges to infrastructure code, once that code is accepted, it automatically triggers a deploy
- ![[images/Pasted image 20240228020951.png]]