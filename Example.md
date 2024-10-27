## Provisioned Capacity

For items up to 4 KB in size, one RCU can perform two eventually consistent read requests per second. 

If we require 100 requests per second, then we need 50 RCU. 

Each RCU has a price of $0.00013 per RCU per Hour. Therefore, our running rate for read is $0.0065 per second.

So the cost for read in a month is:

50 RCU x $0.00013 per hour x 24 hours x 30 days = $4.68