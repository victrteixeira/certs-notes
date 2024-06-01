# Regions, Region Pairs and Sovereign Regions
## Regions
- Areas of the world where Azure has a **set of datacenters** (minimum 3 in a set)
- Not necessarily "countries" but can be
- Usually each region is connected to another region to make a "region pair"
- Region pairs have highest speed connections and special treatment during Azure updates
## Region Pairs
- Many regions also have a paired region to support cross-region replication based on proximity and other factors
- While Azure regions are designed to offer protection against local disasters with AZs, they can also provide protection from regional or large geography disasters
- Making use of another secondary region that uses **cross-region replication** (CRR). Both primary and secondary regions together form a **region pair**
### Example Pairs
- Canada: Canada Central - Canada East
- Europe: North Europe - West Europe
- USA: East US - West US
- USA: East US 2 - Central US
- Brazil: Brazil South - South Central US
## Sovereign Regions
- These are not connected to the Azure Public Cloud
- Require approval to join / create a subscription
- Adhere to different compliance standards
- They're isolated from the rest of Azure and are dedicated to specific entities, such as governments
# Availability Zones and Data Centers
- Azure AZs are physically separe locations within each Azure region
- Each AZ can have one or more datacenters
- Each AZ has its own independent power, cooling, and networking infrastructure
- Not every region supports AZs
	- Canada East does not have AZs, for example
- Not every service supports AZs
- There are **three types of AZ Services**: Zonal Services; Zone-Redundant Services; Always Available Services
	- Some services give you the choice between zonal and zone-redundant
## Zonal Services
- You can choose a specific AZ to deploy the service to
- You then should deploy a duplicate service to another zone to achieve resiliency
- e.g. Virtual Machines
## Zone-Redundant Services
- Automatically deployed across zones for you
- You don't have to configure it
- e.g. Azure SQL Database
## Always Available Services
- These are global services and Microsoft takes care of the ensuring that they are always on
- Also called "Non-regional services"
- e.g. Azure Portal, Azure Active Directory, Azure Front Door

