# AWS SAP Session 2

Date: 2026-06-04
Status: Completed
Cycle: 1

## Source Files

- `02-identity/identity-center.md`
- `03-networking/vpc.md`

## Result

- Question Count: 10
- Correct: 7
- Wrong: 3
- Accuracy: 70%
- Main Weakness: VPC networking fundamentals: IGW public access conditions, Route Table association rules, IPv6 egress design

## Covered Topics

### IAM Identity Center

- IAM Identity Center is the recommended centralized SSO service for workforce users accessing multiple AWS accounts and business applications.
- It can connect users and groups from a built-in identity store, AWS Managed Microsoft AD, on-premises Microsoft AD, or an external SAML 2.0 IdP.
- The identity store is the source of users and groups, not the permission model itself.
- AWS account access is controlled by mapping users/groups to accounts and permission sets.
- When a user logs in, AWS access is provided through temporary AWS credentials rather than long-lived IAM users.
- AWS Organizations is a prerequisite for centralized multi-account access management.

### Public/Private Services and Access Control

- Public/private describes the network reachability model, not whether access is automatically allowed.
- Public endpoints can still be blocked by IAM policy, resource policy, endpoint policy, or network controls.
- Private network paths still require authorization by IAM/resource policies where applicable.
- Final access depends on both network reachability and permission authorization.

### DHCP Option Set

- DHCP Option Set is a VPC-level bundle of DHCP options delivered to instances, mainly DNS/NTP/domain-related configuration.
- A single DHCP Option Set can be associated with zero or more VPCs.
- A VPC can have at most one DHCP Option Set at a time.
- DHCP Option Sets cannot be edited after creation. To change values, create a new option set and associate it with the VPC.
- VPC association changes are immediate, but existing instances usually pick up new values after DHCP renewal.

### VPC Router and Route Tables

- The VPC Router is an implicit AWS-managed router. It exists as VPC routing functionality but is not a user-created router resource.
- Route Tables are the routing rule sets used by the VPC Router.
- Every Route Table has a non-editable local route for the VPC CIDR.
- A subnet can be associated with exactly one Route Table at a time.
- If a custom Route Table association is removed, the subnet falls back to the Main Route Table.
- More specific routes win by longest prefix match.

### Internet Gateway Public Access Conditions

- Attaching an IGW alone does not make an EC2 instance publicly reachable.
- Public internet reachability for IPv4 generally requires: public IPv4/EIP, a subnet route `0.0.0.0/0 -> IGW`, and SG/NACL rules that allow the traffic.

### NACL and Security Group

- NACL applies at subnet level and is stateless.
- NACL supports both ALLOW and DENY rules.
- Security Group applies to ENI and is stateful.
- Security Group supports ALLOW rules only and can reference other Security Groups.

### AWS Local Zones

- Local Zones are AWS-provided local infrastructure, not user-created zones.
- Users opt in to available Local Zones, create subnets in them, and launch supported resources there.
- Local Zones are extensions of a Parent Region, not independent Regions.
- They reduce latency by placing compute/storage resources physically closer to users or on-site systems.
- Not all AWS services are supported in Local Zones; some functions still use the Parent Region.

### Ingress Routing

- Gateway Route Tables can route inbound traffic arriving through gateways such as IGW/VGW to inspection appliances.
- Therefore, the statement “inbound traffic cannot be controlled by route tables” can be false in ingress routing scenarios.

### IPv6 in VPC

- IPv6 addresses are globally routable by default; the IPv4-style public/private distinction does not apply in the same way.
- AWS NAT Gateway and NAT Instance do not support IPv6 NAT.
- Egress-only Internet Gateway is used for IPv6 outbound-only internet access while blocking inbound initiation.
- IGW and EIGW are separate resources and can coexist in the same VPC.

### HA Subnet Design

- HA means High Availability: designing so the service continues during failures.
- In AWS, HA commonly means spreading resources across multiple AZs.
- Subnet count is commonly calculated as: tiers × AZ count.
- An internet-facing ALB must be placed in public subnets, while its EC2 targets can be in private subnets.

## Quiz Review

### Score

- Correct: 7/10
- Wrong: 3/10
- Accuracy: 70%

### Wrong Questions

#### Q3

- My Answer: B
- Correct Answer: D
- Weak Tag: IGW internet access conditions
- Why Wrong: The question asked for the least likely explanation. Missing public IPv4/EIP is a valid reason for public EC2 access failure. The incorrect statement is that attaching an IGW alone automatically enables internet access.
- Learned Point: Public IPv4/EIP + `0.0.0.0/0 -> IGW` route + SG/NACL allow rules are required together for public IPv4 reachability.

#### Q5

- My Answer: A
- Correct Answer: B
- Weak Tag: Route Table subnet association rule
- Why Wrong: A subnet cannot be associated with multiple Route Tables at the same time. Each subnet uses exactly one Route Table.
- Learned Point: Every Route Table has a non-editable local route, and each subnet can be associated with only one Route Table.

#### Q10

- My Answer: A
- Correct Answer: C
- Weak Tag: IPv6 egress-only IGW vs NAT
- Why Wrong: AWS NAT Gateway/Instance does not support IPv6 NAT. IPv6 outbound-only access uses Egress-only Internet Gateway.
- Learned Point: For IPv6 outbound-only internet access, use EIGW, not NAT Gateway.

## User Questions Logged

### IAM Identity Center / ID Store / SAML 2.0

- IAM Identity Center centrally manages workforce SSO to multiple AWS accounts and applications.
- ID Store is the source of users and groups.
- SAML 2.0 is the federation protocol used to pass authentication assertions from an external IdP to a service provider.
- Identity Center standardizes and centralizes traditional SAML-based workforce access patterns.

### Public/Private and Policy Control

- Public/private is about reachability path.
- IAM/resource/network policies determine authorization and filtering.
- Access generally requires both network reachability and permission authorization.

### DHCP Option Set

- DHCP Option Set is a VPC-level network configuration bundle delivered through DHCP.
- It is especially important when EC2 instances must use custom DNS such as on-premises AD DNS.

### VPC Router

- It is called implicit because AWS provides router functionality automatically inside the VPC, but users do not create or manage a router resource.

### Local Zones

- Users do not create Local Zones; AWS provides them.
- Users opt in, create Local Zone subnets, and place supported resources there.
- Low latency comes from physical proximity and shorter network paths.

### HA

- HA means High Availability.
- AWS HA designs commonly distribute resources across multiple AZs.

## Next Session

- Not assigned yet
