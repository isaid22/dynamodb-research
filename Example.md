## On-demand Capacity Read

For items up to 4KB in size, one RRU can perform two eventual consistent read.

One RRU's price is $0.25 per million read request units

Assume read request at 100 requests per second:

the number of requests in a month is:

100 request per second x 60 seconds per minute x 60 minutes per hour x 24 hours per day x 30 days per month = 259.2 millions request 

Total cost for read on-demand DynamoDB table in a month is:

259.2 million reads x $0.25 per million reads = $64.8


## Provisioned Capacity Read

For items up to 4 KB in size, one RCU can perform two eventual consistent read requests per second. 

If we require 100 requests per second, then we need 50 RCU. 

Each RCU has a price of $0.00013 per RCU per Hour. Therefore, our running rate for read is $0.0065 per second.

So the cost for read provisioned DynamoDB table in a month is:

50 RCU x $0.00013 per hour x 24 hours x 30 days = $4.68