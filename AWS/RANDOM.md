# RANDOM

## Instantiating Applications quickly

- When launching a full stack (EC2, EBS, RDS), it can take time to:
- Install applications
- Insert initial (or recovery) data
- Configure everything
- Launch the application

> We can take advantage of the cloud to speed that up!

- EC2 Instances:
  - **Use a Golden AMI**: Install your applications, OS dependencies, etc., beforehand and launch your EC2 instance from the Golden AMI
  - **Bootstrap using User Data**: For dynamic configuration, use User Data scripts
  - **Hybrid**: mix Golden AMI and User Data (Elastic Beanstalk)
- RDS Databases:
  - Restore from a snapshot: the database will have schemas and data ready!
- EBS Volumes:
  - Restore from a snapshot: the disk will already be formatted and have data!

## Elastic Beanstalk – Overview

- Elastic Beanstalk is a developer-centric view of deploying an application on AWS
- It uses all the components we’ve seen before: EC2, ASG, ELB, RDS, …
- Managed service
  - Automatically handles capacity provisioning, load balancing, scaling, application health monitoring, instance configuration, …
  - Just the application code is the responsibility of the developer
- We still have full control over the configuration
- Beanstalk is free, but you pay for the underlying instances

### Components

- **Application**: collection of Elastic Beanstalk components (environments, versions, configurations, …)
- **Application Version**: an iteration of your application code
- **Environment**
  - Collection of AWS resources running an application version (only one application version at a time)
  - **Tiers**: Web Server Environment Tier & Worker Environment Tier
  - You can create multiple environments (dev, test, prod, …)

### Elastic Beanstalk – Supported Platforms

- Go
- Java SE
- Java with Tomcat
- .NET Core on Linux
- .NET on Windows Server
- Node.js
- PHP
- Python
- Ruby
- Packer Builder
- Single Container Docker
- Multi-container Docker
- Preconfigured Docker

### Web Server Tier vs. Worker Tier

### Elastic Beanstalk Deployment Modes
