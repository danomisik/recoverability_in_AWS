# Data durability and recovery

In this project you will create highly available solutions to common use cases.  You will build a Multi-AvailabilityZone, Multi-Region database and show how to use it in multiple geographically separate AWS regions.  You will also build a website hosting solution that is versioned so that any data destruction and accidents can be quickly and easily undone.

## Getting Started

To get started, clone this repo.  Aside from instructions, it contains a CloudFormation script to build an AWS VPC with public and private subnets.  It also contains an example website that you will host in an AWS S3 bucket in your account.

## Project Instructions
### Cloud formation
In this project, you will use the AWS CloudFormation to create Virtual Private Clouds. CloudFormation is an AWS service that allows you to create "infrastructure as code". This allows you to define the infrastructure you'd like to create in code, just like you do with software. This has the benefits of being able to share your infrastructure in a common language, use source code control systems to version your infrastructure and allows for documenting and reviewing of infrastructure and infrastructure proposed changes.

CloudFormation allows you to use a configuration file written in a YAML file to automate the creation of AWS resources such as VPCs. In this project, you will use a pre-made CloudFormation template to get you started. This will allow you to create some of the infrastructure that you'll need without spending a lot of time learning details that are beyond the scope of this course.

You can find the YAML file in the GitHub repo: https://github.com/udacity/nd063-c2-design-for-availability-resilience-reliability-replacement-project-starter-template/blob/master/cloudformation/vpc.yaml

In order to build a VPC from the YAML file, follow the steps:

1. Services -> CloudFormation
2. Create stack “With new resources (standard)”
3. Template is ready
4. Upload a template file
5. Click “Choose file” button
6. Select provided YAML file
7. Next
8. Fill in Stack name
9. Name the VPC
10. Update the CIDR blocks
11. Click Next
12. Click Next again
13. Click Create stack
14. Wait for the stack to build out.  Refresh until status becomes “CREATE_COMPLETE”
15. Observe the “Outputs” tab for the created IDs.  These will be used later.

Once the CloudFormation Stack has completed, you can look at the "Resources" tab to see all of the AWS resources that the stack has created.  You can see both the type of resources that have been created, as well as the AWS identifiers for those resources so that you can locate these resources in the AWS service that they are a part of.

The "Outputs" tab shows you custom output from the CloudFormation Stack that is labeled and described for you.  These descriptions are custom descriptions that were added to the CloudFormation template and make it easier for you to find specific values that have been created as a part of the CloudFormation stack.  Here, you can find the VPC ID that has been created, the subnet IDs including which subnets are public and which are private, and the Security Groups that have been created and a description of each.

### Part 1
Complete the following steps:
### Data durability and recovery
In order to achieve the highest levels of durability and availability in AWS you must take advantage of multiple AWS regions. 
1. Pick two AWS regions. An active region and a standby region.
2. Use CloudFormation to create one VPC in each region. Name the VPC in the active region "Primary" and name the VPC in the standby region "Secondary".

**NOTE**: Be sure to use different CIDR address ranges for the VPCs.
**SAVE** screenshots of both VPCs after they are created. Name your screenshots: primary_Vpc.png, secondary_Vpc.png


### Highly durable RDS Database
1. Create a new RDS Subnet group in the active and standby region.
2. Create a new MySQL, multi-AZ database in the active region. The database must:
     - Be a “burstable” instance class.
     - Have only the “UDARR-Database” security group.
     - Have an initial database called “udacity.”
3. Create a read replica database in the standby region. This database has the same requirements as the database in the active region. 

**SAVE** screenshots of the configuration of the databases in the active and secondary region after they are created. 
**SAVE** screenshots of the configuration of the database subnet groups as well as route tables associated with those subnets. Name the screenshots: primaryDB_config.png, secondaryDB_config.png, primaryDB_subnetgroup.png, secondaryDB_subnetgroup.png, primaryVPC_subnets.png, secondaryVPC_subnets.png, primary_subnet_routing.png, secondary_subnet_routing.png


### Estimate availability of this configuration
Write a paragraph or two describing the achievable Recovery Time Objective (RTO) and Recovery Point Objective (RPO) for this Multi-AZ, multi-region database in terms of:

1. Minimum RTO for a single AZ outage
2. Minimum RTO for a single region outage
3. Minimum RPO for a single AZ outage
4. Minimum RPO for a single region outage

**SAVE** your answers in a text file named "estimates.txt"

### Demonstrate normal usage
In the active region:
1. Create an EC2 keypair in the region
2. Launch an Amazon Linux EC2 instance in the active region. Configure the instance to use the VPC's public subnet and security group ("UDARR-Application"). 
3. SSH to the instance and connect to the "udacity" database in the RDS instance. 
4. Verify that you can create a table, insert data, and read data from the database. 
5. You have now demonstrated that you can read and write to the primary database

**SAVE** the log of connecting to the database, creating the table, writing to and reading from the table in a text file called "log_primary.txt"

### Monitor database
1. Observe the “DB Connections” to the database and how this metric changes as you connect to the database
2. Observe the “Replication” configuration with your multi-region read replica. 

**SAVE** screenshots of the DB Connections and the database replication configuration. Name your screenshots: monitoring_connections.png, monitoring_replication.png

### Part 2
### Failover And Recovery
In the standby region:

1. Create an EC2 keypair in the region
2. Launch an Amazon Linux EC2 instance in the standby region. Configure the instance to use the VPC's public subnet and security group ("UDARR-Application").
3. SSH to the instance and connect to the read replica database.
4. Verify if you are not able to insert data into the database but are able to read from the database.
5. You have now demonstrated that you can only read from the read replica database.

**SAVE** log of connecting to the database, writing to and reading from the table in a text file called "log_rr_before_promotion.txt"

**SAVE** screenshot of the database configuration now, before promoting the read replica database in the next step. Name your screenshot: rr_before_promotion.png

6. Promote the read replica
7. Verify that if you are able to insert data into and read from the read replica database.
8. You have now demonstrated that you can read and write the promoted database in the standby region.

**SAVE** log of connecting to the database, writing to and reading from the database in a text file named "log_rr_after_promotion.txt"

**SAVE** screenshots of the database configuration after the database promotion. Name your screenshot: rr_after_promotion.png

### Part 3
### Website Resiliency

Build a resilient static web hosting solution in AWS. Create a versioned S3 bucket and configure it as a static website.

1. Enter “index.html” for both Index document and Error document
2. Upload the files from the GitHub repo (under `/project/s3/`)
3. Paste URL into a web browser to see your website. 

**Save** the screenshot of the webpage. Name your screenshot "s3_original.png"
You will now “accidentally” change the contents of the website such that it is no longer serving the correct content

You will now “accidentally” change the contents of the website such that it is no longer serving the correct content

1. Change `index.html` to refer to a different “season”
2. Re-upload `index.html`
3. Refresh web page

**SAVE** a screenshot of the modified webpage. Name your screenshot "s3_season.png"

You will now need to “recover” the website by rolling the content back to a previous version.

1. Recover the `index.html` object back to the original version
2. Refresh web page

**SAVE** a screenshot of the modified webpage. Name your screenshot "s3_season_revert.png"

You will now “accidentally” delete contents from the S3 bucket. Delete “winter.jpg”

**SAVE** screenshots of the modified webpage and of the existing versions of the file showing the "Deletion marker". Name your screenshots: s3_deletion.png, s3_delete_marker.png

You will now need to “recover” the object:

1. Recover the deleted object
2. Refresh web page

**SAVE** a screenshot of the modified webpage. Name your screenshot "s3_delete_revert.png"

## Recoverability in AWS - FINISHED PROJECT

Below is description finalized project based on specification.

### Relational Database Resilience

1. SWBAT build networks that will continue to operate through the loss of a single data center

Primary VPC was created from cloudformation script:
![Primary VPC](screenshots/primary_VPC.PNG "Primary VPC")

Secondary VPC was created from cloudformation script:
![Secondary VPC](screenshots/secondary_VPC.PNG "Secondary VPC")

2. SWBAT build systems that align to a business availability objectives for redundancy.

Configuration of Primary RDS is:
![Primary DB config 1](screenshots/primaryDB_config.PNG "Primary DB config 1")

![Primary DB config 2](screenshots/primaryDB_config2.PNG "Primary DB config 2")

Primary RDS subnet group is:
![Primary RDS subnet group](screenshots/primaryVPC_subnetgroup.PNG "Primary RDS subnet group")

Subnets in subnet group:
![Primary RDS subnet 1](screenshots/primaryVPC_subnet1.PNG "Primary RDS subnet 1")
![Primary RDS subnet 2](screenshots/primaryVPC_subnet2.PNG "Primary RDS subnet 2")

Routing tables for  configured database subnets are:
![Primary RDS routing table 1](screenshots/primary_subnet_routing1.PNG "Primary RDS routing table 1")
![Primary RDS routing table 2](screenshots/primary_subnet_routing2.PNG "Primary RDS routing table 2")

3. SWBAT build systems that align to business availability objectives for resiliency.

Configuration of read replica is:
![Secondary DB config 1](screenshots/secondaryDB_config1.PNG "Secondary DB config 1")

![Secondary DB config 2](screenshots/secondaryDB_config2.PNG "Secondary DB config 2")

Secondary RDS subnet group is:
![Secondary RDS subnet group](screenshots/secondaryDB_subnetgroup.PNG "Secondary RDS subnet group")

Subnets details:
![Subnet 1 details](screenshots/secondaryVPC_subnet1.PNG "Subnet 1 details")
![Subnet 2 details](screenshots/secondaryVPC_subnet2.PNG "Subnet 2 details")

Subnets route tables details:
![Secondary subnet routing 1](screenshots/secondary_subnet_routing1.PNG "Secondary subnet routing 1")
![Secondary subnet routing 2](screenshots/secondary_subnet_routing2.PNG "Secondary subnet routing 2")

### Manage applications in AWS

1. SWBAT predict the availability of a configuration

Solution is in "./estimates.txt" file.

2. SWBAT use correct data access patterns.

Logs between Primary RDS and EC2 are in ./log_primary.txt.

Logs between Secondary RDS and EC2 before promotion are in ./log_rr_before_promotion.txt.

Note: VPC peering is setup, thats why we can use same EC2.

3. SWBAT monitor highly available system

Monitoring details of Primary RDS after connections from EC2:
![Monitoring connections 1](screenshots/monitoring_connections1.PNG "Monitoring connections 1")
![Monitoring connections 2](screenshots/monitoring_connections2.PNG "Monitoring connections 2")

Database replica configuration:
![Monitoring replication](screenshots/monitoring_replication.PNG "Monitoring replication")

4. SWBAT operate a highly resilient database

Logs between Secondary RDS and EC2 before promotion are in ./log_rr_before_promotion.txt.

Secondary RDS configuration before promotion:
![Secondary RDS before promotion 1](screenshots/rr_before_promotion.PNG "Secondary RDS before promotion 1")
![Secondary RDS before promotion 2](screenshots/rr_before_promotion2.PNG "Secondary RDS before promotion 2")

Logs between Secondary RDS and EC2 after promotion are in ./log_rr_after_promotion.txt.

Secondary RDS configuration after promotion:
![Secondary RDS after promotion 1](screenshots/rr_after_promotion1.PNG "Secondary RDS after promotion 1")
![Secondary RDS after promotion 2](screenshots/rr_after_promotion2.PNG "Secondary RDS after promotion 2")

### Website recovery

1. SWBAT create a versioned website

Screenshot of the website with a winter scene as the background and displaying a timestamp:
![S3 original](screenshots/s3_original.PNG "S3 original")

2. SWBAT recover from “accidental” modification to website

Screenshot of same website with a different season (picture) as the background, still displaying a timestamp:
![S3 season](screenshots/s3_season.PNG "S3 season")

Screenshot of AWS S3 object “index.html” showing multiple versions of the object exist:
![S3 season - multiversion](screenshots/s3_season_multi_version.PNG "S3 season - multiversion")

Screenshot of the same website once again with the original background, still displaying a timestamp:

![S3 season revert](screenshots/s3_season_revert.PNG "S3 season revert")

![S3 season revert - versions](screenshots/s3_season_revert_versions.PNG "S3 season revert - versions")

Screenshot of the same website with no background image:
![S3 deletion](screenshots/s3_deletion.PNG "S3 deletion")

Screenshot of AWS S3 object “winter.jpg” showing multiple versions of the object exist with the latest being a “deletion marker”:
![S3 delete marker](screenshots/s3_delete_marker.PNG "S3 delete marker")

Screenshot of the same website once again with the original background, still displaying a timestamp:
![S3 delete revert](screenshots/s3_delete_revert.PNG "S3 delete revert")

### Suggestions to Make Your Project Stand Out!

1. Student creates RDS database via CloudFormation or Terraform

Cloudfromation script with Primary RDS is in ./cloudformation/primary_vpc.yaml.

Cloudfromation script with Read replica of RDS is in ./cloudformation/secondary_vpc.yaml

Configuration of Primary RDS through AWS Console after creation is below:
![CloudFormation Primary RDS config 1](screenshots/CloudFormation_PrimaryRDS_config1.PNG "CloudFormation PrimaryRDS config 1")
![CloudFormation Primary RDS config 2](screenshots/CloudFormation_PrimaryRDS_config2.PNG "CloudFormation PrimaryRDS config 2")

Configuration of Read replica of Primary RDS through AWS Console after creation is below:
![CloudFormation Secondary RDS config 1](screenshots/CloudFormation_SecondaryRDS_config1.PNG "CloudFormation Secondary RDS config 1")
![CloudFormation Secondary RDS config 2](screenshots/CloudFormation_SecondaryRDS_config2.PNG "CloudFormation Secondary RDS config 2")

2. Student sets up VPC peering between the two regions

![VPC peering in Primary region side](screenshots/vpc_peering_frankfurt.PNG "VPC peering in Primary region side")

![VPC peering in Secondary region side](screenshots/vpc_peering_stockholm.PNG "VPC peering in Secondary region side")

Routing tables for subnets in both VPCs needed to be changed after VPC peering. Example of route table change in one of those subnets is below:
![VPC peering - example of route table](screenshots/vpc_peering_example_changed_root_table.PNG "VPC peering - example of route table")

Because I wanted to create connection between EC2 in primary region and Secondary RDS in secondary region I needed also change security group for Secondary RDS. See below:
![VPC peering - changed security group for Secondary RDS](screenshots/vpc_peering_changed_security_group.PNG "VPC peering - changed security group for Secondary RDS")

3. Student sets up S3 bucket behind a CloudFront distribution

I created CloudFront for S3 bucket. Settings are below:
![CloudFront settings 1](screenshots/CloudFront_settings1.PNG "CloudFront settings 1")
![CloudFront settings 2](screenshots/CloudFront_settings2.PNG "CloudFront settings 2")

To verify CloudFront is working, I deleted content in S3 bucket and tried to visit S3 bucket web site endpoint and Cloudfront endpoint.


Empty S3 bucket below:
![Empty S3 bucket](screenshots/CloudFront_S3_settings_after_delete.PNG "Empty S3 bucket")


![S3 bucket web endpoint - not wokring](screenshots/CloudFront_S3_after_delete.PNG "S3 bucket web endpoint - not wokring")

![CloudFront endpoint - wokring](screenshots/CloudFront_after_delete.PNG "CloudFront endpoint - wokring")

## License


## HOW TO

Subpart with HOW to links.

### How to enable permissions for S3 static web hosting:

<https://docs.aws.amazon.com/AmazonS3/latest/dev/WebsiteAccessPermissionsReqd.html>

### How to recover to previous state of object:

1. aws s3api get-object --bucket static-web-site-udacity-project-1 --key index.html --version-id xlp5uHFhsGU7foCXGxrX4m9TtsgsDbiH  index-version1.html
2. aws s3api put-object --bucket static-web-site-udacity-project-1 --key index.html --body index-version1.html --acl public-read --content-type text/html
{
    "ETag": "\"d28fae6944e056e777980636d7118cd2\"",
    "VersionId": "R7V7j1rehbgbSLzzxShk.JfwD9GhAwAP"
}
Note: dont forget set "--content-type text/html". If you will not, webside will not work.

### How to undelete deleted object

<https://docs.aws.amazon.com/AmazonS3/latest/user-guide/undelete-objects.html>

### How to setup CloudFront + S3 bucket

<https://app.pluralsight.com/course-player?clipId=7cf86902-5c52-454f-915b-1db49042d690>

### How to Disaster recovery

Minimum RTO for a single AZ outage:

Try to reboot RDS with failover and you can see how long does it take for the secondary AZ database to become active. Refer this link on how to reboot the database with failover: <https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_RebootInstance.html>

Single AZ outage in multi-AZ deployment: There exists synchronous replication in case of multi AZ deployments. Whenever one AZ fails, automatic failover takes place. RTO and RPO are very less in this case.
Refer this link to understand more about multi-AZ deployments: <https://aws.amazon.com/rds/features/multi-az/>

Single region outage in multi-region deployment: If one region fails, we have to promote the read replica to take place of master database. This requires manual intervention. Also, there exists asynchronous replication in this case. Thus, RTO and RPO are slightly greater in this case.
Refer this link to understand more about RTO and RPO for RDS: <https://aws.amazon.com/blogs/database/implementing-a-disaster-recovery-strategy-with-amazon-rds/>

