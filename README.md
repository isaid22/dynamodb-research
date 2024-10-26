# dynamodb-research

## Compare and document the capacity models and cost

There are two capacity modes available. Each mode has its own cost structure and unit of measures to which the cost is calculated. These capacity modes are: 
1. on-demand capacity mode
2. provisioned capacity mode

### On-demand mode
In on-demand capacity mode, DynamoDB charges user for the data reads and writes performed on the table. One does not need to specify read or write capacity
to start using DynamoDB in this pricing mode. DynamoDB will adjust these capacity based on the workload's requirements.

On-demand capacity is designdd for:
* crete new tables with unknown workloads
* Have unpredictable application traffic
* Prefer paying for only what you use.