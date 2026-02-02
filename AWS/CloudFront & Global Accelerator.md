# CloudFront & Global Accelerator

## Amazon CloudFront

- Content Delivery Network (CDN)
- **Improves read performance, content is cached at the edge**
- Improves users experience
- Hundreds of Points of Presence globally (edge locations, caches)
- **DDoS protection (bc. worldwide), integration with Shield, AWS Web Application Firewall**

### CloudFront – Origins

- **S3 bucket**
  - For distributing files and caching them at the edge
  - For uploading files to S3 through CloudFront
  - Secured using Origin Access Control (OAC)
- **VPC Origin**
  - For applications hosted in VPC private subnets
  - Application Load Balancer / Network Load Balancer / EC2 Instances
- **Custom Origin (HTTP)**
  - S3 website (must first enable the bucket as a static S3 website)
  - Any public HTTP backend you want

### CloudFront at a high level

![[Pasted image 20260129204656.png]]

#### CloudFront – S3 as an Origin

![[Pasted image 20260129204717.png]]

##### CloudFront vs S3 Cross-Region Replication

- CloudFront:
  - Global Edge network
  - Files are cached for a TTL (maybe a day)
  - **Great for static content that must be available everywhere**

  ---

- S3 Cross-Region Replication:
  - Must be set up for each region where you want replication to happen
  - Files are updated in near real-time
  - Read only
  - **Great for dynamic content that needs to be available at low-latency in a few regions**

#### CloudFront – ALB or EC2 as an origin - Using VPC Origins

- Allows you to deliver content from your applications hosted in your VPC private subnets (no need to expose them on the Internet)
- Deliver traffic to **private**:
  - Application Load Balancer
  - Network Load Balancer
  - EC2 Instances

#### CloudFront – ALB or EC2 as an origin - Using Public Network

![[Pasted image 20260129205055.png]]

### CloudFront Geo Restriction

- You can restrict who can access your distribution
  - **Allowlist**: Allow your users to access your content only if they're in one of the countries on a list of approved countries.
  - **Blocklist**: Prevent your users from accessing your content if they're in one of the countries on a list of banned countries.

  ---

- The “country” is determined using a 3rd party Geo-IP database
- Use case: Copyright Laws to control access to content

### CloudFront - Pricing

- CloudFront Edge locations are all around the world
- The cost of data out per edge location varies

### CloudFront – Price Classes

- You can reduce the number of edge locations for cost reduction
- Three price classes:
  1. Price Class All: all regions – best performance
  2. Price Class 200: most regions, but excludes the most expensive regions
  3. Price Class 100: only the least expensive regions

![[Pasted image 20260129205229.png]]

### CloudFront – Cache Invalidations

- In case you update the back-end origin, CloudFront doesn’t know about it and will only get the refreshed content after the TTL has expired
- However, you can force an entire or partial cache refresh (thus bypassing the TTL) by performing a **CloudFront Invalidation**
- You can invalidate all files (\*) or a special path (/images/*)

### Global users for our application

- You have deployed an application and have global users who want to access it directly.
- They go over the public internet, which can add a lot of latency due to many hops
- We wish to go as fast as possible through the AWS network to minimise latency

## Unicast IP vs Anycast IP

- **Unicast IP**: one server holds one IP address
- **Anycast IP**: all servers hold the same IP address, and the client is routed to the nearest one

## AWS Global Accelerator

- Leverage the AWS internal network to route to your application
- 2 **Anycast IPs** are created for your application
- The Anycast IP send traffic directly to Edge Locations
- The Edge locations send the traffic to your application

  ---

- Works with **Elastic IP, EC2 instances, ALB, NLB, public or private**
- Consistent Performance
  - Intelligent routing to the lowest latency and fast regional failover
  - No issue with client cache (because the IP doesn’t change)
  - Internal AWS network
- Health Checks
  - Global Accelerator performs a health check of your applications
  - Helps make your application global (failover less than 1 minute for unhealthy)
  - Great for disaster recovery (thanks to the health checks)
- Security
  - Only 2 external IPs need to be whitelisted
  - DDoS protection thanks to AWS Shield

## AWS Global Accelerator vs CloudFront

- They both use the AWS global network and its edge locations around the world
- Both services integrate with AWS Shield for DDoS protection.

  ---

- **CloudFront**
  - Improves performance for both cacheable content (such as images and videos)
  - Dynamic content (such as API acceleration and dynamic site delivery)
  - Content is served at the edge

  ---

- **Global Accelerator**
  - Improves performance for a wide range of applications over TCP or UDP
  - Proxying packets at the edge to applications running in one or more AWS Regions.
  - Good fit for non-HTTP use cases, such as gaming (UDP), IoT (MQTT), or Voice over IP
  - Good for HTTP use cases that require static IP addresses
  - Good for HTTP use cases that require deterministic, fast regional failover
