## Records
- How you want to route traffic for a domain
- Each record contains:
	- Domain/subdomain Name - e.g. example.com
	- Record Type - e.g. A, AAAA, CNAME
	- Value - e.g. 12.34.45.67
	- Routing Policy - how Route53 responds to queries
	- TTL - amount of time the record cached at DNS Resolvers
- Route 53 supports the following DNS record types
	- A / AAAA / CNAME / NS - MUST KNOW
	- CAA / DS / MX / NAPTR / PTR / SOA / TXT / SPF / SRV - ADVANCED

## Record Types
- A - maps a hostname to IPv4 ip.
- AAAA - maps a hostname to IPv6 ip.
- CNAME - maps a hostname to another hostname
	- The target is a domain name which must have an A or AAAA record
	- Can't create a CNAME record for the top node of a DNS namespace (Zone Apex)
	- Example: you can't create for 'example.com', but you can create for 'www.example.com'
- NS - Name Servers for the Hosted Zone
	- Control how traffic is routed to a domain

## Hosted Zones
- A container for records that define how to route traffic to a domain and its subdomains
- **Public Hosted Zones** - contains records that specify how to route traffic on the internet (public domain names): 'application1.mypublicdomain.com'
- **Private Hosted Zones** - contains records that specify how you route traffic within one or more VPCs (private domain names): 'application1.company.internal'
- You pay $0.50 per month per hosted zone