# dynamodb-research

## Compare and document the capacity models and cost

There are two capacity modes available. Each mode has its own cost structure and unit of measures to which the cost is calculated. These capacity modes are: 
1. on-demand capacity mode
2. provisioned capacity mode

### On-demand Capacity Mode
In on-demand capacity mode, DynamoDB charges user for the data reads and writes performed on the table. One does not need to specify read or write capacity
to start using DynamoDB in this pricing mode. DynamoDB will adjust these capacity based on the workload's requirements.

On-demand capacity mode is for you if you:
* crete new tables with unknown workloads
* Have unpredictable application traffic
* Prefer paying for only what you use.

### Provisioned Capacity Mode
In provisioned capacity mode, a user specifies the number of reads and writes per second that are expected and required. a user will also specify auto scaling capacity to ensure the table's capacity and ensure application's performance.

Provisioned capacity mode is sfor you if you:
* Have predictable tapplication traffic
* Run application's traffic that is consistent or ramps gradually
* Can forecast capacity based on reproducible traffic patterns

Above are general descriptions to each pricing capacity mode. Below are the detailed comparison which will help clarify how cost is calculated for each capacity mode.

## Detailed comparision of Capacity Modes

