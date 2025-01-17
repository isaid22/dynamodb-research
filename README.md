# DynamoDb - Feature and Pricing Model Research

## Compare and document the capacity models and cost

There are two capacity modes available. Each mode has its own cost structure and unit of measures to which the cost is calculated. These capacity modes are: 
1. on-demand capacity mode
2. provisioned capacity mode

### On-demand Capacity Mode
In on-demand capacity mode, DynamoDB charges user for the data reads and writes performed on the table. One does not need to specify read or write capacity
to start using DynamoDB in this pricing mode. DynamoDB will adjust these capacity based on the workload's requirements.

On-demand capacity mode is for you if you:
* Create new tables with unknown workloads
* Have unpredictable application traffic
* Prefer paying for only what you use.

### Provisioned Capacity Mode
In provisioned capacity mode, a user specifies the number of reads and writes per second that are expected and required. a user will also specify auto scaling capacity to ensure the table's capacity and ensure application's performance.

Provisioned capacity mode is for you if you:
* Have predictable application traffic
* Run application's traffic that is consistent or ramps gradually
* Can forecast capacity based on reproducible traffic patterns

Above are general descriptions to each pricing capacity mode. Below are the detailed comparison which will help clarify how cost is calculated for each capacity mode.

## Detailed comparision of Capacity Modes

Since the two capacity modes are intended to meet different requirements, the cost structure and therefore billing units are measured diffeerently. Below are different views of cost structure, and definition of billing units. 

### On-demand Capacity Features and Billing

#### Units of Measure

**Read request unit**: API calls to read data from your table are billed in read request units. DynamoDB read requests can be either strongly consistent, eventually consistent, or transactional. A strongly consistent read request of up to 4 KB requires one read request unit. For items larger than 4 KB, additional read request units are required. For items up to 4 KB in size, an eventually consistent read request requires one-half read request unit, and a transactional read request requires two read request units. For example, a strongly consistent read request of an 8 KB item requires two read request units, an eventually consistent read of an 8 KB item requires one read request unit, and a transactional read of an 8 KB item requires four read request units. See Read Consistency for more details.

**Write request unit**: API calls to write data to your table are billed in write request units. A standard write request unit can write an item up to 1 KB. For items larger than 1 KB, additional write request units are required. A transactional write requires two write request units. For example, a write request of a 1 KB item requires one write request unit, a write request of a 3 KB item requires three write request units, and a transactional write request of a 3 KB item requires six write request units.

**Replicated write request unit**: When using DynamoDB global tables, your data is written automatically to multiple AWS Regions of your choice. Each write occurs in the local Region as well as the replicated Regions.

**Streams read request unit**: Each GetRecords API call to DynamoDB Streams is a streams read request unit. Each streams read request unit can return up to 1 MB of data.

**Transactional read/write requests**: In DynamoDB, a transactional read or write differs from a standard read or write because it guarantees all operations contained in a single transaction set succeed or fail as a set.

**Change data capture units**: DynamoDB can capture item-level changes in your DynamoDB tables and replicate them to other AWS services such as Amazon Kinesis Data Streams and AWS Glue. DynamoDB captures these changes as delegated operations, which means DynamoDB performs the replication on your behalf so that you don’t have to manage throughput capacity. DynamoDB charges one change data capture unit for each write to your table (up to 1 KB). For items larger than 1 KB, additional change data capture units are required.

**DynamoDB table classes**: DynamoDB offers two table classes designed to help you optimize for cost. The DynamoDB Standard table class is the default and recommended for the vast majority of workloads. The DynamoDB Standard-Infrequent Access (DynamoDB Standard-IA) table class is optimized for tables that store data that is accessed infrequently, where storage is the dominant cost. Each table class offers different pricing for data storage as well as read and write requests. You can select the most cost-effective table class based on your table’s storage requirements and data access patterns. Learn more about DynamoDB table classes in the DynamoDB Developer Guide.

#### On-demand Example Bill

Here is the read and write cost to a DynamoDB table, respectively. 

**DynamoDB Standard table class**
| On-demand throughput type  | Cost |
| -------- | ------- |
| Write Request Units (WRU)  | $1.25 per million write request units    |
| Read Request Units (RRU) | $0.25 per million read request units     |


**Data storage**: Assume your table occupies 25 GB of storage at the beginning of the month and grows to 29 GB by the end of the month, averaging 27 GB based on continuous monitoring of your table size. Since your table class is set to DynamoDB Standard, the first 25 GB of storage are included in the AWS Free Tier. The remaining 2 GB of storage are charged at $0.25 per GB, 
resulting in a table storage cost of $0.50 for the month.

**Backup and restore**: If the sum of all your on-demand backup storage were 60 GB for a 30-day month, the monthly cost of your backups would be ($0.10 x 60 GB) = $6.00/month. However, if you delete 15 GB of your on-demand backup data 10 days into the monthly cycle, you are billed ($0.10 x 60 GB) - ($0.10 x 15 GB x 20/30) = $5.00/month. 

Now assume that in addition to performing on-demand backups, you use continuous backups. The average size of your table is 27 GB, resulting in a monthly cost of ($0.20 x 27 GB) = $5.40/month. If you need to restore your table at the end of the month, the cost is ($0.15 x 29 GB) = $4.35.

**Change data capture for Kinesis Data Streams**: Now assume you enable streaming to a Kinesis data stream to process your data changes using Amazon Kinesis services. DynamoDB charges one change data capture unit for each write of 1 KB it captures to the Kinesis data stream. Assuming your application write traffic from earlier in this example is consistent for your Kinesis data stream, this results in 42,177,000 change data capture units over the course of the month. Your monthly cost will be ($0.10 x 42,177,000/1,000,000) = $4.22.

**Data export to Amazon S3**: Let’s say you want to export table backups to Amazon S3 for analysis. If the size of your table at the specified point in time is 29 GB, the resulting export costs are: ($0.10 x 29 GB) = $2.90.

**Integration with DynamoDB Accelerator (DAX)**: DAX is an Amazon DynamoDB-compatible caching service. You have determined that you need to accelerate the response time of your application and decide to use the DynamoDB Accelerator (DAX) service. You review the available hardware specifications and determine that a three-node cluster of the t2.small instance type will suit your needs. You enable DAX on day 26. DynamoDB charges $0.12 per hour ($0.04 x 3 nodes), totaling $14.40 for the final 5 days in the month ($0.12 x 120 hours).

**Global tables**: Now assume you choose to create a disaster recovery replica table in the US West (Oregon) Region. Assume that you add the replica in the US West (Oregon) Region when your table is 25 GB in size, resulting in $3.75 ($0.15 x 25 GB) of table restore charges. Adding this replica also generates 25 GB of data transfer, as detailed under the "Data transfer" section below. Also assume that your application traffic from earlier in this example is consistent for your global table. You will consume 84.35 million replicated write request units (42,177,000 writes x 2 Regions), resulting in a charge of $158.16 (84.35 million replicated write request units x $1.875 per million). You also store an additional 27 GB of data in your replicated table in the US West (Oregon) Region. The first 25 GB of storage are included in the AWS Free Tier in each AWS Region for tables using the DynamoDB Standard table class. The remaining 2 GB of storage are charged at $0.25 per GB, resulting in additional table storage cost of $0.50 for the month.

**DynamoDB Streams**: Assume you enable DynamoDB Streams and build your application to perform one read request per second against the streams data. Over the course of a month, this results in 2,592,000 streams read requests, of which the first 2,500,000 read requests are included in the AWS Free Tier. You pay only for the remaining 92,000 read requests, which are $0.02 per 100,000 read request units.

**Data transfer**: Because you are transferring data between AWS Regions for your global tables implementation, DynamoDB charges for data transferred out of the Region but not for inbound data transfer. Assuming 42,177,000 writes of 1 KB each in the month, you would generate 40.22 GB in data to transfer between Regions. Adding the replica in the US West (Oregon) Region generates an additional 25 GB of data transfer. You will be charged $5.94 ($0.09 x [41 + 25]) GB) for inter-region data transfer.

In summary, your total monthly charges for a single-Region on-demand DynamoDB table are:

* Write traffic: $52.72
* Read traffic: $10.54
* Data storage: $0.50
* On-demand backup: $5.00
* Continuous (PITR) backup: $5.40
* Table restore: $4.35
* Change data capture for Kinesis Data Streams: $4.22
* Data export to Amazon S3: $2.90
* Integration with DynamoDB Accelerator (DAX), an Amazon DynamoDB-compatible caching service: $14.40
* DynamoDB Streams: $0.02

Total charges: $100.05 
All prices are based on region: US East (N.Virginia)

### Provisioned Capacity Features and Billing

Below are definitions for units of measure for a dynamoDB table in Provisioned Capacity mode:

#### Units of Measure

**Read capacity unit (RCU)**: Each API call to read data from your table is a read request. Read requests can be strongly consistent, eventually consistent, or transactional. For items up to 4 KB in size, one RCU can perform one strongly consistent read request per second. Items larger than 4 KB require additional RCUs. For items up to 4 KB in size, one RCU can perform two eventually consistent read requests per second. Transactional read requests require two RCUs to perform one read per second for items up to 4 KB. For example, a strongly consistent read of an 8 KB item would require two RCUs, an eventually consistent read of an 8 KB item would require one RCU, and a transactional read of an 8 KB item would require four RCUs. See Read Consistency for more details.

**Write capacity unit (WCU)**: Each API call to write data to your table is a write request. For items up to 1 KB in size, one WCU can perform one standard write request per second. Items larger than 1 KB require additional WCUs. Transactional write requests require two WCUs to perform one write per second for items up to 1 KB. For example, a standard write request of a 1 KB item would require one WCU, a standard write request of a 3 KB item would require three WCUs, and a transactional write request of a 3 KB item would require six WCUs.

**Replicated write capacity unit (rWCU)**: When using DynamoDB global tables, your data is written automatically to multiple AWS Regions of your choice. Each write occurs in the local Region as well as the replicated Regions.

**Streams read request unit**: Each GetRecords API call to DynamoDB Streams is a streams read request unit. Each streams read request unit can return up to 1 MB of data.

**Transactional read/write requests**: In DynamoDB, a transactional read or write differs from a standard read or write because it guarantees that all operations contained in a single transaction set succeed or fail as a set.

**Change data capture units**: DynamoDB can capture item-level changes in your DynamoDB tables and replicate them to other AWS services such as Amazon Kinesis Data Streams and AWS Glue. DynamoDB captures these changes as delegated operations, which means DynamoDB performs the replication on your behalf so that you don’t have to manage throughput capacity. DynamoDB charges one change data capture unit for each write to your table (up to 1 KB). For items larger than 1 KB, additional change data capture units are required.

**DynamoDB table classes**: DynamoDB offers two table classes designed to help you optimize for cost. The DynamoDB Standard table class is the default and recommended for the vast majority of workloads. The DynamoDB Standard-Infrequent Access (DynamoDB Standard-IA) table class is optimized for tables that store data that is accessed infrequently, where storage is the dominant cost. Each table class offers different pricing for data storage as well as read and write requests. You can select the most cost-effective table class based on your table’s storage requirements and data access patterns. Learn more about DynamoDB table classes in the DynamoDB Developer Guide.

#### Provisioned Example Bill

**DynamoDB Standard table class**
| On-demand throughput type  | Price per hour |
| -------- | ------- |
| Write Capacity Units (WCU)  | $0.00065 per WCU  |
| Read Capacity Units (RCU) | $0.00013 per RCU    |


**Data storage**: Assume your table occupies 25 GB of storage at the beginning of the month and grows to 29 GB by the end of the month, averaging 27 GB based on the continuous monitoring of your table size. Since your table class is set to DynamoDB Standard, the first 25 GB of storage are included in the AWS Free Tier. The remaining 2 GB of storage are charged at $0.25 per GB, resulting in a table storage cost of $0.50 for the month.

**Backup and restore**: If the sum of all your on-demand backup storage is 60 GB for a 30-day month, the monthly cost of your backups is ($0.10 x 60 GB) = $6.00/month. However, if you then delete 15 GB of your on-demand backup data 10 days into the monthly cycle, you are billed ($0.10 x 60 GB) - ($0.10 x 15 GB x 20/30) = $5.00/month.

Now assume that in addition to performing on-demand backups, you use continuous backups. The size of your table is 29 GB, resulting in a monthly cost of ($0.20 x 29 GB) = $5.80/month.

If you need to restore your 29 GB table once during the month, that restore costs ($0.15 x 29 GB) = $4.35.

**Change data capture for Kinesis Data Streams**: Now assume you enable streaming to a Kinesis data stream to process your data changes using Amazon Kinesis services. Also assume that your write throughput is consistent with the previous example. Your application performs 80 writes of 1 KB per second. DynamoDB charges one change data capture unit for each write of 1 KB it captures to the Kinesis data stream. Over the course of a month, this results in (80 x 3,600 x 24 x 30) = 207,360,000 change data capture units. Your monthly cost will be ($0.10 x 207,360,000/1,000,000) = $20.74.

**Data export to Amazon S3**: Let’s say you want to export table backups to Amazon S3 for analysis. If the size of your table at the specified point in time is 29 GB, the resulting export costs are: ($0.10 x 29 GB) = $2.90.

**Integration with DynamoDB Accelerator (DAX)**: DAX is an Amazon DynamoDB-compatible caching service. You have determined that you need to accelerate the response time of your application and decide to use the DynamoDB Accelerator (DAX) service. You review the available hardware specifications and determine that a three-node cluster of the t2.small instance type suits your needs. You enable DAX on day 26. DynamoDB charges $0.12 per hour ($0.04 x 3 nodes), totaling $14.40 for the final 5 days in the month ($0.12 x 120 hours).

**Global tables**: Now assume you create a disaster recovery replica table in the US West (Oregon) Region. Assume that you add the replica in the US West (Oregon) Region when your table is 25 GB in size, resulting in $3.75 ($0.15 x 25 GB) of table restore charges. Adding this replica also generates 25 GB of data transfer, as detailed under the "Data transfer" section below. Also assume that your capacity needs are consistent with the previous example. Auto scaling continues to provision 114 WCUs and 114 RCUs for your application's throughput needs, but it now must also provision rWCUs for writing to both of your replica tables. Provisioned rWCUs equal the total number of rWCUs needed for application writes in both Regions. In this scenario, you now perform 80 writes per second to both the US East (N. Virginia) Region and the US West (Oregon) Region, resulting in a minimum provisioned capacity of 160 rWCUs (80 rWCUs in N. Virginia + 80 rWCUs in Oregon = 160 rWCUs). Auto scaling provisions 229 rWCUs (160 rWCUs/70%) to maintain actual utilization at 70 percent of provisioned capacity. For more information, see Best Practices and Requirements for Managing Global Tables. Your first 25 provisioned rWCUs provisioned each hour in each Region are included in the AWS Free Tier for tables using the DynamoDB Standard table class, resulting in an hourly charge of $0.174525, or $125.66 in a 30-day month. You also store an additional 27 GB of data in your replicated table in the US West (Oregon) Region. The first 25 GB of storage are included in the AWS Free Tier in each AWS Region for tables using the DynamoDB Standard table class. The remaining 2 GB of storage are charged at $0.25 per GB, resulting in additional table storage cost of $0.50 for the month.

**DynamoDB Stream**s: Now assume you enable DynamoDB Streams and build your application to perform one read request per second against the streams data. Over the course of a month, this results in 2,592,000 streams read requests, of which the first 2,500,000 read requests are included in the AWS Free Tier. You pay only for the remaining 92,000 read requests, which are $0.02 per 100,000 read request units.

**Data transfer**: Because you are now transferring data between AWS Regions for your global tables implementation, DynamoDB charges for data transferred out of the Region, but it does not charge for inbound data transfer. Assuming a constant 80 writes per second of 1 KB each, you generate 80 KB per second in data transfer between Regions, resulting in 198 GB (80 KB per second x 2,592,000 seconds in a 30-day month) of cross-Region data transfer per month. Adding the replica in the US West (Oregon) Region generates an additional 25 GB of data transfer. If you have already used your AWS Free Tier data transfer allowance on other AWS services, you will be charged $20.07 ($0.09 x [198 GB + 25 GB]) for data transfer.

Therefore, the total monthly charges for a single-Region provisioned DynamoDB table are:

Provisioned capacity: $50.00
Data storage: $0.50
On-demand backup: $5.00
Continuous (PITR) backup: $5.80
Table restore: $4.35
Change data capture for Kinesis Data Streams: $20.74
Data export to Amazon S3: $2.90
Integration with DynamoDB Accelerator (DAX), an Amazon DynamoDB-compatible caching service: $14.40
DynamoDB Streams: $0.02
Total charges: $103.71





## Factors affecting RRU (ON-demand) and RCU (Provisioned)

| Factors | RRU (On-demand) | RCU (Provisioned) | 
| -------- | ------- | ------- |
| Item or record size  | :white_check_mark:   | :white_check_mark: |
| Read consistency |   :white_check_mark:    | :white_check_mark: |
| Frequency of request |  :white_check_mark:    | :x: |
| Throughput |  :x:    | :white_check_mark: |
| Burst Capacity |  :x:    | :white_check_mark: |
| Auto Scaling |  :x:    | :white_check_mark: |

## Expore Cost Optimization Techniques for DynamoDB

There are several cost optimization strategies for DynamoDB that apply to both on-demand and provisioned capacity modes. Here’s a breakdown of these universal strategies:

**1. Optimize Data Modeling and Access Patterns**
* Use Efficient Data Access Patterns: Design your table and indexes to support the primary access patterns for your application with `Query` operations instead of `Scan` whenever possible, as `Scan` is more resource-intensive.
* Minimize Item Size: Keep item size as small as possible. Since both modes calculate costs based on item size (4 KB increments), smaller items lead to lower costs.

**2. Reduce Indexes and Use Projected Attributes**
Limit Global Secondary Indexes (GSIs): Each GSI incurs additional read and write costs. Only create GSIs when absolutely necessary to support access patterns.
Project Only Needed Attributes: Configure GSIs to project only the attributes you need for queries. This reduces storage costs and read costs across both modes by limiting the data processed per request.

**3. Set Up Time-to-Live (TTL) for Data Expiration**
Automatically Delete Expired Data: Enable TTL for items that have a defined expiration period (e.g., session data, logs). This reduces storage costs and avoids paying for unnecessary read/write capacity on stale data.

**4. Use Batch Operations for Efficiency**
BatchGetItem and BatchWriteItem: When possible, use batch operations to process multiple items in a single request, reducing the number of requests needed. Batch requests are more efficient than multiple single-item operations.

**5. Use Application-Level Caching**
Integrate Caching: Implement a caching layer (e.g., Amazon ElastiCache, DynamoDB Accelerator (DAX)) for frequently accessed data. This reduces the frequency of read requests to DynamoDB, thus lowering RRU/RCU consumption in both modes.

**6. Optimize Read Consistency**
Use Eventually Consistent Reads: Unless your application specifically requires the latest data, opt for eventually consistent reads. This choice reduces read costs by 50% as eventually consistent reads are half the cost of strongly consistent reads in both modes.

**7. Implement Efficient Pagination**
Limit Data Per Request with Pagination: When querying large datasets, use pagination by setting a Limit parameter on requests. This prevents retrieving large amounts of data in a single read operation, reducing RRUs/RCUs used per request.

**8. Monitor and Analyze Usage with CloudWatch**
Track Capacity Usage: Set up CloudWatch alarms for read and write usage, as well as throttling metrics. Monitoring these metrics helps you adjust settings, improve access patterns, or identify costly requests, leading to better capacity and cost management in both modes.

**9. Consider Hybrid Capacity Modes for Specific Use Cases**
Separate Tables for Different Workloads: For applications with a combination of predictable and unpredictable traffic, use a hybrid approach where predictable traffic is managed by provisioned tables and unpredictable traffic by on-demand tables. This allows you to control costs more effectively for each workload.

These strategies are applicable to either on-demand or provisioned capacity mode. 

[Working Example](Example.md)

[Read Consistency](ReadConsistency.md)