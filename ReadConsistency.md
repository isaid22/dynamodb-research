| Read Consistency Type | Overview | Performance | Use Cases |
| ---------------------- | ---------------------- | ---------------------- | ---------------------- | 
| Eventual Consistency | Returns data that may not reflect the most recent writes, but is generally accurate. | Faster and more cost-efficient; consumes half the Read Request Units (RRUs) or Read Capacity Units (RCUs). | Suitable for analytics, reporting, or user-generated content feeds. |
|Strong Consistency | Returns the most up-to-date data, reflecting all completed writes. | More expensive; consumes 1 RRU/RCU for each 4 KB read; slightly slower due to consistency checks. | Essential for scenarios where data accuracy is critical, such as financial transactions or real-time inventory management. | 
| Transaction Consistency | Ensures that a set of operations (both reads and writes) either all succeed or all fail, maintaining a consistent state. | May incur additional costs due to the complexity of operations; requires more resources. | Useful for complex applications requiring multiple items to be updated in a single, atomic operation, such as booking systems or financial transfers. |

Recommendation: use Eventual Consistency for use case that doesn't involve actively update or write records.